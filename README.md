import sys
from typing import Tuple

LETTER = 0
DIGIT = 1
UNKNOWN = 99

# Tokens
INT_LIT = 10
IDENT = 11
ASSIGN_OP = 20
ADD_OP = 21
SUB_OP = 22
MULT_OP = 23
DIV_OP = 24
LEFT_PAREN = 25
RIGHT_PAREN = 26
EOF = -1

class Lexer:
    def __init__(self, input_string: str):
        self.input = input_string
        self.pos = 0
        self.char_class = UNKNOWN
        self.lexeme = []
        self.next_token = None
        self.next_char = ''
        
        self.get_char()

    def get_char(self):
        if self.pos < len(self.input):
            self.next_char = self.input[self.pos]
            self.pos += 1

            if self.next_char.isalpha():
                self.char_class = LETTER
            elif self.next_char.isdigit():
                self.char_class = DIGIT
            else:
                self.char_class = UNKNOWN
        else:
            self.char_class = EOF
            self.next_char = ''

    def add_char(self):
        if len(self.lexeme) < 100:
            self.lexeme.append(self.next_char)
        else:
            print("Error - lexeme is too long")

    def get_non_blank(self):
        while self.next_char.isspace() and self.char_class != EOF:
            self.get_char()

    def lookup(self, ch: str) -> int:
        token = EOF
        if ch == '(':
            self.add_char()
            token = LEFT_PAREN
        elif ch == ')':
            self.add_char()
            token = RIGHT_PAREN
        elif ch == '+':
            self.add_char()
            token = ADD_OP
        elif ch == '-':
            self.add_char()
            token = SUB_OP
        elif ch == '*':
            self.add_char()
            token = MULT_OP
        elif ch == '/':
            self.add_char()
            token = DIV_OP
        return token

    def lex(self) -> Tuple[int, str]:
        self.lexeme = []
        self.get_non_blank()

        if self.char_class == LETTER:
            self.add_char()
            self.get_char()
            while self.char_class == LETTER or self.char_class == DIGIT:
                self.add_char()
                self.get_char()
            token = IDENT

        elif self.char_class == DIGIT:
            self.add_char()
            self.get_char()
            while self.char_class == DIGIT:
                self.add_char()
                self.get_char()
            token = INT_LIT

        elif self.char_class == UNKNOWN:
            token = self.lookup(self.next_char)
            self.get_char()

        elif self.char_class == EOF:
            token = EOF
            self.lexeme = ['E', 'O', 'F']

        lexeme_str = ''.join(self.lexeme)
        print(f"Next token is: {token}, Next lexeme is {lexeme_str}")
        return token, lexeme_str

if __name__ == "__main__":
    input_string = "(sum - 87) /  (sum *9)total"
    lexer = Lexer(input_string)

    while True:
        token, lexeme = lexer.lex()
        if token == EOF:
            break
