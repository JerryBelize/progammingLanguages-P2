# progammingLanguages-P2

load "stdlib.ring"

;; MAIN Program
func main()
    while true
        display_bnf_grammar()
        print("Enter input string ('END' to exit): ")
        input = read()

        if input == "END"
            break
        
        if leftmost_derivation(input)
            draw_parse_tree(input)
            generate_pbasic_program(input)
        
        print("Press any key to continue...")
        input = read() ;; Pause before looping again

;; Display the BNF Grammar
func display_bnf_grammar()
    print("BNF Grammar for iZEBOT meta-language:\n")
    print("<program> ::= 'do' <commands> 'done'\n")
    print("<commands> ::= <command> <commands> | <command>\n")
    print("<command> ::= 'Button' <key> <movement> ';'\n")
    print("<key> ::= 'A' | 'B' | 'C' | 'D'\n")
    print("<movement> ::= 'MoveF' | 'MoveB' | 'TurnL' | 'TurnR' | 'SpinL' | 'SpinR'\n")

;; Leftmost Derivation Subprogram
func leftmost_derivation(input)
    print("Performing leftmost derivation...\n")
    
    ;; Check for the correct structure: must start with "do" and end with "done"
    if input contains "do" and input contains "done"
        ;; Process the input commands
        commands = extract_commands(input)
        if valid_commands(commands)
            print("Derivation successful! Input is valid.\n")
            ;; Perform leftmost derivation on the commands
            leftmost_command_derivation(commands)
            return true
        else
            print("Error: Invalid command in the input string.\n")
            return false
    else
        print("Error: Input must start with 'do' and end with 'done'.\n")
        return false

;; Perform leftmost derivation on commands
func leftmost_command_derivation(commands)
    for command in commands
        key, movement = command[1], command[2]
        print("<command> → Button {key} {movement};")
        print("<key> → {key}")
        print("<movement> → {movement}")
        ;; Perform leftmost expansion for each part of the command
    print("Leftmost derivation complete.\n")

;; Validate the commands
func valid_commands(commands)
    for key, movement in commands
        if not (key in ["A", "B", "C", "D"] and movement in ["MoveF", "MoveB", "TurnL", "TurnR", "SpinL", "SpinR"])
            return false
    return true

;; Parse Tree Subprogram
func draw_parse_tree(input)
    print("Drawing parse tree for input...\n")
    print("Program -> do Commands done\n")
    print("Commands -> Command Commands\n")
    print("Command -> Button A MoveF;\n")
    ;; You can expand this to display more detailed parse trees

;; PBASIC Code Generation Subprogram
func generate_pbasic_program(input)
    print("Generating PBASIC program...\n")
    
    ;; HEADER block code
    header = "{\$STAMP BS2p}\n{\$PBASIC 2.5}\nKEY VAR Byte\nMain: DO\nSERIN 3,2063,250,Timeout,[KEY]\n"
    
    ;; BODY block code - based on input commands
    body = generate_body(input)
    
    ;; FOOTER and SUBROUTINE blocks
    footer1 = "LOOP\nTimeout: GOSUB Motor_OFF\nGOTO Main\n"
    footer2 = "Motor_OFF: LOW 13 : LOW 12 : LOW 15 : LOW 14 : RETURN\n"
    
    ;; Combine blocks into full PBASIC program
    pbasic_program = header + body + footer1 + generate_subroutines(input) + footer2
    
    print("Generated PBASIC Program:\n", pbasic_program)
    
    ;; Save the program to a BSP file
    save_file("IZEBOT.BSP", pbasic_program)
    print("PBASIC Program saved as IZEBOT.BSP\n")

;; Helper Functions for Command Parsing
func extract_commands(input)
    ;; Extract Button x y pairs from the input string
    ;; Example return: [("A", "MoveF"), ("B", "TurnL")]
    return [("A", "MoveF"), ("B", "TurnL")]

func generate_body(input)
    body = ""
    commands = extract_commands(input)
    
    for key, movement in commands
        body += "IF KEY = '{key}' THEN GOSUB {movement_subroutine(movement)}\n"
    
    return body

;; Generate Subroutines Based on Movements
func movement_subroutine(movement)
    switch movement
        case "MoveF" return "Forward"
        case "MoveB" return "Backward"
        case "TurnL" return "TurnLeft"
        case "TurnR" return "TurnRight"
        case "SpinL" return "SpinLeft"
        case "SpinR" return "SpinRight"

func generate_subroutines(input)
    subroutines = ""
    commands = extract_commands(input)
    
    for key, movement in commands
        subroutines += case_subroutine(movement)
    
    return subroutines

func case_subroutine(movement)
    switch movement
        case "MoveF" return "Forward: HIGH 13 : LOW 12 : HIGH 15 : LOW 14 : RETURN\n"
        case "MoveB" return "Backward: HIGH 12 : LOW 13 : HIGH 14 : LOW 15 : RETURN\n"
        case "TurnL" return "TurnLeft: HIGH 13 : LOW 12 : LOW 15 : LOW 14 : RETURN\n"
        case "TurnR" return "TurnRight: LOW 13 : LOW 12 : HIGH 15 : LOW 14 : RETURN\n"
        case "SpinL" return "SpinLeft: HIGH 13 : LOW 12 : HIGH 14 : LOW 15 : RETURN\n"
        case "SpinR" return "SpinRight: HIGH 12 : LOW 13 : HIGH 15 : LOW 14 : RETURN\n"
