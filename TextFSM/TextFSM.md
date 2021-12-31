## Template file

The template file is the description of how the FSM should parse out data. Individual templates are required for different patterns of textual input. For example parsing output from router commands generally requires one template for each command.

A full example template follows. In this case, to process the above 'show chassis routing-engine' command output from a router:

```
# Chassis value will be null for single chassis routers.
Value Filldown Chassis (.cc.?-re.)
Value Required Slot (\d+)
Value State (\w+)
Value Temp (\d+)
Value CPUTemp (\d+)
Value DRAM (\d+)
Value Model (\S+)

# Allway starts in 'Start' state.
Start
  ^${Chassis}
  # Record current values and change state.
  # No record will be output on first pass as 'Slot' is 'Required' but empty.
  ^Routing Engine status: -> Record RESlot

# A state transition was not strictly necessary but helpful for the example.
RESlot
  ^\s+Slot\s+${Slot}
  ^\s+Current state\s+${State}
  ^\s+Temperature\s+${Temp} degrees
  ^\s+CPU temperature\s+${CPUTemp} degrees
  ^\s+DRAM\s+${DRAM} MB
  # Transition back to Start state.
  ^\s+Model\s+${Model} -> Start

# An implicit EOF state outputs the last record.
```

The template file consists of two top level sections.

- The 'Value' definitions, which describe the columns of data to extract.
- One or more 'State' definitions, describing the various states of the engine whilst parsing data.

A line is considered a comment if it starts with any optional white space then a hash i.e matches regular expression: `"^\s*#"`.

### Value definitions

One or more 'Value' lines are used to describe each column that will be in the resulting table. These Value lines must all appear before any state definitions and must be contiguous lines, separated only by comments.

Each Value line is of the following format:

*Value* [option[,option...]] name regex

| **Keyword** | **Type**                           | **Description**                                              |
| ----------- | ---------------------------------- | ------------------------------------------------------------ |
| **Value**   | Keyword                            | Indicates that this is a Value line entry, mandatory.        |
| *option*    | Flags, comma separated (no spaces) | Extra options regarding the value. May be one or more of: **Filldown** The previously matched value is retained for subsequent records (unless explicitly cleared or matched again). In other words, the most recently matched value is copied to newer rows unless matched again. **Key** Declares that the fields contents contribute to the unique identifier for a row. **Required** The record (row) is only saved into the table if this value is matched. **List** The value is a list, appended to on each match. Normally a match will overwrite any previous value in that row. **Fillup** Like Filldown, but populates upwards until it finds a non-empty entry. Not compatible with Required. |
| *name*      | Value name                         | The name of the Value, which will end up as the column name. Must not be the name of a valid option. |
| *regex*     | A regex                            | The regex against which the Value will match. This regex must be contained within parenthesis. |

### State definitions

After the Value definitions, the State definitions are described. Each state definition is separated by a blank line. The first line is the state name, an alphanumeric word followed by a series of rules.

A state definition is of the following format:

```
stateName
 ^rule
 ^rule
 ...
```

Multiple state definitions are to be separated by at least one blank line. Rules are described on consecutive lines immediately following the state name and must be indented by one or two white spaces and a carat ('^').

Initially, the FSM will begin at the **Start** state. Input is only compared to the current state but a matched line can trigger a transition to a new state. Evaluation continues line by line until either **EOF** is encountered or the current state transitions to the **End** state.

### Reserved states

The FSM starts in state **Start**, so this label is mandatory and the template will not parse without it.

If *EOF* was reached on the input then the **EOF** state is executed. This is an implicit state that looks like:

```
EOF
  ^.* -> Record
```

**EOF** records the current row before returning. To override this behavior - explicitly define an empty **EOF** state. like so:

```
EOF
```

The **End** state is reserved and terminates processing of input lines and does not execute the **EOF** state.

### State Rules

Each state definition consists of a list of one or more rules. The FSM reads a line from the input buffer and tests it against each rule, in turn, starting from the top of the current state. If a rule matches the line, then the action is carried out and the process repeats (from the top of the state again) with the next line.

Rules are of the following format:

**^***regex* [**->** *action*]

*regex* is a regular expression compared against input lines. The match is performed from the start of the input line, so the carat ('^') although implicit, is required syntax as a reminder of this behavior.

The regex may contain zero or more Value descriptors. Value descriptors are in the format *$ValueName* or *${ValueName}* (the latter format is preferred) and indicate value assignment. The regex of the associated value is substituted into the rule regex, and if the line matches, the text that matches this Value is assigned to the current row. To indicate the end of line (EOL) use a double dollar sign '$$', this will be substituted for a single dollar sign during Value substitution.

For example, take the following template:

```
Value Interface (\S+)

Start
  ^Interface ${Interface} is up
```

When initially parsing the template, the FSM will expand the rule's regex to:

```
^Interface (\S+) is up
```

If the following line is parsed through this rule, then the value 'Interface' is given the value *GigabitEthernet1/10*:

```
Interface GigabitEthernet1/10 is up.
```

Multiple value substitutions may be placed into a rule regex. The entire expanded regex must match, for any Values to be assigned.

### Rule Actions

Following a regexp, actions may be described, delimited by '->' and are of the format **'A.B C'**.

Actions are broken down into three optional parts. A) Line Actions, actions on the input line. B) Record Actions, actions on the values collected so far. C) State transition.

If actions are not described i.e. no '->', then the default implicit action is **Next.NoRecord**.

#### Line Actions

| **Action**   | **Description**                                              |
| ------------ | ------------------------------------------------------------ |
| **Next**     | Finish with the input line, read in the next and start matching again from the start of the state. This is the default behavior if no line action is specified. |
| **Continue** | Retain the current line and do not resume matching from the first rule of the state. Continue processing rules as if a match did not occur (value assignments still occur). |

#### Record Actions

After the line action is the optional record action, these are separated by a full stop '.'.

| **Action**   | **Description**                                              |
| ------------ | ------------------------------------------------------------ |
| **NoRecord** | Do nothing. This is the default behavior if no record action is specified. |
| **Record**   | Record the values collected so far as a row in the return data. Non Filldown values are cleared. Note: No record will be output if there are any 'Required' values that are unassigned. |
| **Clear**    | Clear non Filldown values.                                   |
| **Clearall** | Clear all values.                                            |

The dot '.' separator is only required if both line and record actions are specified. If one or both are left as the implicit default then the dot is omitted i.e. **Next**, **Next.NoRecord** and **NoRecord** are equivalent.

#### New State Transition

The action can be optionally followed by white spaces and a new State. The State must be one of the reserved states or a valid state defined in the template. Upon matching, after any actions are performed normally, the next line is read from input and the current state is then changed to the new state and processing continues in this new state.

Note that the **Continue** action does not accept a state transition. This ensures that state machines are loop free.

`在正则表达式之后可以跟着一个空格和一个新的State，就是前面说的C，如果输入的文本匹配到这个本行则转向新的State进行匹配。如下图中的Start中的跟随的Routes表示匹配到了"-----"之后才开始进行下面Routes中的规则匹配。`

#### Error Action

There is a special action 'Error'. This action will terminate all processing and will not return the table, discarding all rows collected so far, and raises an exception.

The syntax for this action is:

**^***regex* **-> Error** [*word|"string"*]