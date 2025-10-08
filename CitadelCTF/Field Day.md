# Field Day
The challenge requires decoding a binary string from the transmission.txt file. 
The description points towards a specific encoding: the military version of UNIVAC FIELDATA, which is based on a 12-row punched card system. 
The key is to interpret 12-bit chunks of the binary data as columns on a punched card and handle special case-shifting characters unique to the military variant.

## My solve
**Flag:** `citadel{r3b3ll10n$&BU1lt:0nH0p3}`
My first step was to research "UNIVAC FIELDATA" based on the prompt. This led me to discover its connection to 12-row punched cards, where each character is represented by a combination of punches in a single column. This perfectly matched the "12 bit transmission" detail, suggesting each 12-bit chunk represents one character.

The most critical clue was the mention of a "military" transmission. Standard FIELDATA doesn't have lowercase letters. However, the military version of the code includes special characters to shift between uppercase and lowercase. I found that the punch combinations for 11-5-8 and 12-5-8 act as toggles for LOWER CASE and UPPER CASE respectively.

With this understanding, I developed a Python script to automate the decoding process. The script does the following:

Creates a dictionary mapping the 12-bit binary patterns to their corresponding ASCII characters, based on the FIELDATA lookup table.

Defines the specific binary codes for shifting to upper and lower case.

Reads the binary string from the provided file.

Iterates through the string, processing it in 12-bit chunks.

Maintains a case_mode variable, which is updated whenever a case-shifting code is encountered.

For every other code, it finds the corresponding character in the dictionary and applies the correct case before appending it to the result.

This is the Python script I used to decode the message:

```# Create a reverse mapping from binary code to character
DECODE_TABLE = {
    '000000000110': '@',   # 7-8
    '100000000110': '#',   # 12-7-8
    '010000000110': '\n',  # 11-7-8 (Carriage Return)
    '000000000000': ' ',   # (Space)
    '100100000000': 'A',   # 12-1
    '100010000000': 'B',   # 12-2
    '100001000000': 'C',   # 12-3
    '100000100000': 'D',   # 12-4
    '100000010000': 'E',   # 12-5
    '100000001000': 'F',   # 12-6
    '100000000100': 'G',   # 12-7
    '100000000010': 'H',   # 12-8
    '100000000001': 'I',   # 12-9
    '010100000000': 'J',   # 11-1
    '010010000000': 'K',   # 11-2
    '010001000000': 'L',   # 11-3
    '010000100000': 'M',   # 11-4
    '010000010000': 'N',   # 11-5
    '010000001000': 'O',   # 11-6
    '010000000100': 'P',   # 11-7
    '010000000010': 'Q',   # 11-8
    '010000000001': 'R',   # 11-9
    '001010000000': 'S',   # 0-2
    '001001000000': 'T',   # 0-3
    '001000100000': 'U',   # 0-4
    '001000010000': 'V',   # 0-5
    '001000001000': 'W',   # 0-6
    '001000000100': 'X',   # 0-7
    '001000000010': 'Y',   # 0-8
    '001000000001': 'Z',   # 0-9
    '100000100010': ')',   # 12-4-8
    '010000000000': '-',   # 11
    '100000000000': '+',   # 12
    '100000001010': '<',   # 12-6-8
    '000001000010': '=',   # 3-8
    '000000001010': '>',   # 6-8
    '000010000010': '&',   # 2-8
    '010001000010': '$',   # 11-3-8
    '010000100010': '*',   # 11-4-8
    '001000100010': '(',   # 0-4-8
    '001000010010': '%',   # 0-5-8
    '000000010010': ':',   # 5-8
    '101000000000': '?',   # 12-0
    '011000000000': '!',   # 11-0
    '001001000010': ',',   # 0-3-8
    '001000001010': '\\',  # 0-6-8 (End of Message)
    '001000000000': '0',   # 0
    '000100000000': '1',   # 1
    '000010000000': '2',   # 2
    '000001000000': '3',   # 3
    '000000100000': '4',   # 4
    '000000010000': '5',   # 5
    '000000001000': '6',   # 6
    '000000000100': '7',   # 7
    '000000000010': '8',   # 8
    '000000000001': '9',   # 9
    '000000100010': "'",   # 4-8
    '010000001010': ';',   # 11-6-8
    '001100000000': '/',   # 0-1
    '100001000010': '.',   # 12-3-8
}

# Case shift codes
UPPER_CASE = '100000010010'  # 12-5-8
LOWER_CASE = '010000010010'  # 11-5-8

def decoder(bin_str):
    if len(bin_str) % 12 != 0:
        return "Error: Binary string length must be a multiple of 12"
    
    result = []
    case_mode = 'UPPER' # Start in uppercase by default
    
    # Process the binary string in 12-bit chunks
    for i in range(0, len(bin_str), 12):
        pattern = bin_str[i:i+12]
        
        if pattern == UPPER_CASE:
            case_mode = 'UPPER'
        elif pattern == LOWER_CASE:
            case_mode = 'LOWER'
        elif pattern in DECODE_TABLE:
            char = DECODE_TABLE[pattern]
            # Apply case change only if the character is an alphabet letter
            if char.isalpha() and case_mode == 'LOWER':
                char = char.lower()
            result.append(char)
            
    return ''.join(result)

# To run the script, first save it as solve.py
# Then, use the following command in your terminal:
# cat transmission.txt | python solve.py
if __name__ == "__main__":
    try:
        binary_data = input().strip()
        decoded_message = decoder(binary_data)
        print(f"Decoded Message: {decoded_message}")
    except EOFError:
        print("Error: No input received. Please pipe the file content.")
```
Running the script with the contents of transmission.txt as input produced the flag.

```
cat transmission.txt | python3 solve.py
Decoded Message: r3b3ll10n$&BU1lt:0nH0p3
```

## What I learned

This challenge was a great introduction to historical data encoding formats. 
I learned about the UNIVAC FIELDATA character set and its direct relationship with the physical layout of 12-row punched cards. 
The most interesting part was discovering the existence of a military-specific version of the code that ingeniously added lowercase capabilities through special case-shifting characters, a feature absent in the standard commercial version. 
It was a practical lesson in how character encodings can have variants for different applications.

## References 

I used the following reference to get a description of the FIELDATA character set. This was essential for building the decoding dictionary.
https://www.fourmilab.ch/documents/univac/fieldata.html
