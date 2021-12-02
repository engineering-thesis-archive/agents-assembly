# Agents Assembly

## Table of Contents

- [Comments](#comments)
- [Agents](#agents)
- [Parameters](#parameters)
- [Behaviours](#behaviours)
- [Actions](#actions)
- [Variables](#variables)
- [Conditional instructions](#conditional_instructions)
- [Operations](#operations)


## Comments <a name = "comments"></a>
Comments are ignored by the translator.

* `#` - starts a single line comment

## Agents <a name = "agents"></a>
Agents are defined in the outer scope of the file. </br>

* `AGENT` `name` - starts an agent definition.

The definition must be ended with `EAGENT`.

```
AGENT average_user

...

EAGENT
```

## Parameters <a name = "parameters"></a>
Parameters are defined in the agent outer scope.</br>
Each agent has read-only access to the following implicit parameters:</br>

* `connCount` - the number of all agent's connections.

Additional parameters can be defined in the following way:

* `PRM` `name`, `args` - adds a parameter to an agent.
    * `PRM` `name`, `float`, `init`, `value` - a float parameter with an intial `value`.
    * `PRM` `name`, `float`, `dist_normal`, `mean`, `std_dev` - a float parameter taken from the normal distribution with `mean` and `std_dev`.
    * `PRM` `name`, `enum`, `val1`, `val1%`, `...` - an enumerable parameter with values `(val1, ...)` and corresponding percentages of the probability of starting with a certain value `(val1%, ...)`.
    * `PRM` `name`, `list`, `conn_list` - a list of agent connections.
    * `PRM` `name`, `list`, `msg_list` - a list of messages.

```
AGENT ...

    PRM minutes_spent_daily_on_youtube, float, init, 205.7

    PRM fakenews_susceptibility, float, dist_normal, 15, 32

    PRM prefered_website, enum, facebook, 100, twitter, 0

    PRM is_online, enum, yes, 95, no, 5

    PRM friends, list, conn_list

    PRM spam, list, msg_list

EAGENT
```

## Behaviours <a name = "agents"></a>
Behaviours are defined in the agent outer scope. They are started in the order of the definitions.</br>

* `SETUPBEHAV` `name` - starts a setup behaviour definition which will run only once, after the agent starts.

The definitions must be ended with `EBEHAV`.

```
AGENT ...

    ...

    SETUPBEHAV setup_average_user

    ...

    EBEHAV

EAGENT
```

## Actions <a name = "actions"></a>
Actions are defined in the behaviour outer scope. They are run in the order of the definitions.</br>

* `ACTION` `name` - starts an action definition.

The definitions must be ended with `EACTION`.

```
AGENT ...

    ...

    SETUPBEHAV ...

        ACTION check_minutes_spent_daily_on_youtube

        ...

        EACTION

        ACTION ...

        ...

        EACTION

    EBEHAV

EAGENT
```

## Variables <a name = "variables"></a>
Variables can be declared in the `ACTION` context.</br>
* `DECL` `name`, `value` - declares a variable with `name` (which is unique in current action scope and is different from names declared in the agent parameters) and `value`, where `value` is declared in the current agent scope or is a valid float.

```
AGENT ...

    ...

    SETUPBEHAV ...

        ACTION ...

            DECL threshold, 100

            DECL num_connections, connCount

        EACTION

        ...

    EBEHAV

EAGENT
```

## Conditional instructions <a name = "conditional_instructions"></a>
Conditional instructions can be used in the `ACTION` context.</br>
* If `arg1` is `...` `arg2`
    * `GT` `arg1`, `arg2` - greater than
    * `LTE` `arg1`, `arg2` - less than or equal to

Every conditional instruction starts a local scope which can be treated as unnamed action so the same rules apply (or in other words - every action is just a named scope). They must be ended with `EBLOCK`.

```
AGENT ...

    ...

    PRM minutes_spent_daily_on_youtube, ...

    ...

    SETUPBEHAV ...

        ACTION ...

            DECL threshold, ...

            DECL num_connections, ...

            GT minutes_spent_daily_on_youtube, threshold
                
                # threshold and num_connections are visible in this scope

                DECL tmp, ...

                LTE ...

                    # tmp, threshold and num_connections are visible in this scope

                    ...

                EBLOCK

            EBLOCK

            # tmp is no longer visible in this scope

            ...

        EACTION

        ...

    EBEHAV

EAGENT
```

## Operations <a name = "operations"></a>
Operations can be used in the `ACTION` context.</br>

* `op` `arg1`, `arg2` - applies `op` to `arg1` and `arg2` and saves the result to `arg1` (`arg1` = `arg1` `op` `arg2`). `arg1` must be mutable.
    * `MULT` `arg1`, `arg2` - multiply
    * `SUBT` `arg1`, `arg2` - subtract

```
AGENT ...

    ...

    SETUPBEHAV ...

        ACTION ...

            DECL threshold, 100

            DECL num_connections, connCount

            # legal because num_connections is a copy of connCount
            SUBT num_connections, threshold

            # illegal because connCount is a read-only agent parameter
            MULT connCount, 15

        EACTION

        ...

    EBEHAV

EAGENT
```
