# SimulationFramework
Proof-of-concept for a simulation language parser and interpreter written in Swift 3 with Xcode 8.

The idea is that a person can write only the equations for a system and the interpreter will handle all of the details of numerical integration, programming, input and output.

The program is a command line tool that runs a simulation program and produces a CSV file with the results. The CSV file can be opened directly with a spreadsheet program.

    Usage: Simulator source [destination]
        source = path to source code file.
        destination = path for results CSV file.
    If destination is not specified, then results are written to standard output.

Programming and simulation let people design and run experiments quickly to develop understanding of a problem. Simulation has four great benefits:

1.	Simulation is active. People build models and test them.
2.	Simulation models make assumptions explicit. 
3.	Simulation models make invisible things visible. The internal workings of real systems are opaque. Simulation can make all parts of a model visible.
4.	Simulation is dynamic and can help build intuition. Very few people can predict the results of changes in even simple systems that have exponential growth or feedback of any kind. Yet these dominate the processes in physics, physiology, sociology, politics and the environment.

The language is line oriented and includes variables, integrals and derivatives. It requires names for each quantity. The language and interpreter also include many features designed to help people avoid common problems with simulation modeling.

Here is an example program:

	# Simple rocket.
	mass_of_rocket = 100
	thrust = if time < stop / 2 then 1000 else 0
	gravity = 9.8 * mass_of_rocket
	acceleration = (thrust - gravity) / mass_of_rocket
	velocity = integral of acceleration, initial 0
	altitude = integral of velocity, initial 0, minimum 0

There are many more programs in the SamplePrograms folder.

    Simulation Language Specification
    Extended Backus-Naur Formalism (EBNF)
    Copyright © 2016 by Thomas Engel. All rights reserved.

    program = line {line_end line} [line_end].
    line = [statement] ["#" comment].
    statement = setting_statement | variable_statement | integral_statement | derivative_statement.
    setting_statement = ("start" | "stop" | "interval" | "print") "=" number.
    variable_statement = name "=" expression {"," modifier}.
    integral_statement = name "=" "integral of" expression {"," modifier}.
    derivative_statement = name "=" "derivative of" expression {"," modifier}.
    modifier = "initial" expression | "minimum" expression  | "maximum" expression  | "label" string | "units" string.

    expression = or_expression | "if" or_expression "then" or_expression "else" or_expression.
    or_expression = and_expression {"or" and_expression}.
    and_expression = not_expression {"and" not_expression}.
    not_expression = ["not"] comparison_expression.
    comparison_expression = add_expression [("=" | "<" | ("<=" | "≤") | ">" | (">=" | "≥")) add_expression].
    add_expression = multiply_expression {("+" | "-") multiply_expression}.
    multiply_expression = exponent_expression {(("*" | "×")" | ("/" | "÷")) exponent_expression}.
    exponent_expression = negative_expression [("^" | "↑")" negative_expression].
    negative_expression = ["-"] value.
value = number | name | function_name "(" expression {"," expression} ")" | "(" expression ")".

    name = letter {letter | digit | "." | "_"}.
	function_name = "sin" | "cos" | "tan" | "arctan" | "ln" | "sqrt" | ("integer" | "int") | ("fraction" | "frac") | ("absolute" | "abs") | ("factorial" | "fact") | ("degrees" | "deg") | ("radians" | "rad")
    number = ["-"] digit {digit} ["." digit {digit}] ["E" ["-"] digit {digit}].
    string = QUOTE {character} QUOTE.
    comment = {character | QUOTE}.
    line_end = LF | CR | CR LF.
    white_space = SPACE | TAB.

Order of operations: negation > exponentiation > multiplication, division > addition, subtraction > comparison > not > and > or > if then else.

Reserved words: integral, derivative, of, true, false, if, then, else, and, or, not, sin, cos, tan, arctan, ln, sqrt, integer, int, fraction, frac, absolute, abs, degrees, deg, radians, rad, pi, e, time, start, stop, interval, print.
