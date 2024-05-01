# Ex-3 GENERATION OF LEXICAL TOKENS USING C
# AIM
## To write a C program to implement lexical analyzer to recognize a few patterns.
# ALGORITHM
1)	Start the program.
2)	Get the input from the user with the terminating symbol ‘;’.
3)	Until the symbol is ‘;’, do the following:
   
    a.	If the next character of the symbol is an operator then print it as an operator.
  	
    b.	If the next character of the symbol is a variable, then insert it into symbol table and print it as an identifier.
  	
    c.	If the next character of the symbol is a bracket, then print it as parenthesis.
  	
5)	Stop the program.
# PROGRAM
```
#include<stdio.h>
#include<ctype.h>
#include<stdlib.h>
#include<string.h>

#define SIZE 128
#define NONE -1
#define EOS '\0'
#define NUM 256
#define KEYWORD 257
#define PAREN_OPEN 296
#define PAREN_CLOSE 297
#define ID 259
#define ASSIGN 317
#define DONE 262
#define MAX 999

char lexemes[MAX];
char buffer[SIZE];
int lastchar = -1;
int lastentry = 0;
int tokenval = NONE;
int lineno = 1;

struct entry {
    char *lexptr;
    int token;
} symtable[100];

struct entry keywords[] = {
    {"if", KEYWORD},
    {"else", KEYWORD},
    {"for", KEYWORD},
    {"int", KEYWORD},
    {"float", KEYWORD},
    {"double", KEYWORD},
    {"char", KEYWORD},
    {"struct", KEYWORD},
    {"return", KEYWORD},
    {0, 0}
};

void errormsg(char *m) {
    fprintf(stderr, "line %d:%s\n", lineno, m);
    exit(1);
}

int lookup(char s[]) {
    int k;
    for (k = lastentry; k > 0; k = k - 1)
        if (strcmp(symtable[k].lexptr, s) == 0)
            return k;
    return 0;
}

int insert(char s[], int tok) {
    int len;
    len = strlen(s);
    if (lastentry + 1 >= MAX)
        errormsg("symtable is full");
    if (lastentry + len + 1 >= MAX)
        errormsg("lexemes array is full");
    lastentry = lastentry + 1;
    symtable[lastentry].token = tok;
    symtable[lastentry].lexptr = &lexemes[lastchar + 1];
    lastchar = lastchar + len + 1;
    strcpy(symtable[lastentry].lexptr, s);
    return lastentry;
}

void initialise() {
    struct entry *ptr;
    for (ptr = keywords; ptr->token; ptr++)
        insert(ptr->lexptr, ptr->token);
}

int lexer() {
    int t;
    int val, i = 0;
    while (1) {
        t = getchar();
        if (t == ' ' || t == '\t');
        else if (t == '\n')
            lineno = lineno + 1;
        else if (t == '(')
            return PAREN_OPEN;
        else if (t == ')')
            return PAREN_CLOSE;
        else if (isdigit(t)) {
            ungetc(t, stdin);
            scanf("%d", &tokenval);
            return NUM;
        } else if (isalpha(t)) {
            while (isalnum(t)) {
                buffer[i] = t;
                t = getchar();
                i = i + 1;
                if (i >= SIZE)
                    errormsg("compiler error");
            }
            buffer[i] = EOS;
            if (t != EOF)
                ungetc(t, stdin);
            val = lookup(buffer);
            if (val == 0)
                val = insert(buffer, ID);
            tokenval = val;
            return symtable[val].token;
        } else if (t == EOF)
            return DONE;
        else {
            tokenval = NONE;
            return t;
        }
    }
}

int main() {
    int lookahead;
    printf("\n \t \t Program for lexical analysis \n\n");
    initialise();
    printf("Enter the expression & place;at the end \n ");
    lookahead = lexer();
    while (lookahead != DONE) {
        if (lookahead == NUM) {
            printf("\n Number :%d", tokenval);
        }
        if (lookahead == '+' || lookahead == '-' || lookahead == '*' || lookahead == '/') {
            printf("\n Operator:%c", lookahead);
        }
        if (lookahead == PAREN_OPEN)
            printf("\n Parenthesis:%c", lookahead);
        if (lookahead == PAREN_CLOSE)
            printf("\n Parenthesis:%c", lookahead);
        if (lookahead == ID)
            printf("\n Identifier:%s", symtable[tokenval].lexptr);
        if (lookahead == KEYWORD)
            printf("\n Keyword");
        if (lookahead == ASSIGN)
            printf("\nAssignment Operator:%c", lookahead);
        if (lookahead == '<' || lookahead == '>' || lookahead == '<=' || lookahead == '>=' || lookahead == '!=') 
            printf("\nRelational Operator");
        lookahead = lexer();
    }
    return 0;
}
```

# OUTPUT
![Screenshot 2024-03-27 092758](https://github.com/Bakkiyalakshmiethiraj/Ex-3-GENERATION-OF-LEXICAL-TOKENS-/assets/144870983/1cdae10b-8f97-401d-a5bd-7b19bd64eb85)

# RESULT
### The program to implement lexical analyzer is executed and the output is verified.
