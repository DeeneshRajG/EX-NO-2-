## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER

 

## AIM:
To write a C program to implement the Playfair Substitution technique.

## DESCRIPTION:

The Playfair cipher starts with creating a key table. The key table is a 5×5 grid of letters that will act as the key for encrypting your plaintext. Each of the 25 letters must be unique and one letter of the alphabet is omitted from the table (as there are 25 spots and 26 letters in the alphabet).

To encrypt a message, one would break the message into digrams (groups of 2 letters) such that, for example, "HelloWorld" becomes "HE LL OW OR LD", and map them out on the key table. The two letters of the diagram are considered as the opposite corners of a rectangle in the key table. Note the relative position of the corners of this rectangle. Then apply the following 4 rules, in order, to each pair of letters in the plaintext:
1.	If both letters are the same (or only one letter is left), add an "X" after the first letter
2.	If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively
3.	If the letters appear on the same column of your table, replace them with the letters immediately below respectively
4.	If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair.
## EXAMPLE:
![image](https://github.com/Hemamanigandan/EX-NO-2-/assets/149653568/e6858d4f-b122-42ba-acdb-db18ec2e9675)

 

## ALGORITHM:

STEP-1: Read the plain text from the user.
STEP-2: Read the keyword from the user.
STEP-3: Arrange the keyword without duplicates in a 5*5 matrix in the row order and fill the remaining cells with missed out letters in alphabetical order. Note that ‘i’ and ‘j’ takes the same cell.
STEP-4: Group the plain text in pairs and match the corresponding corner letters by forming a rectangular grid.
STEP-5: Display the obtained cipher text.




## Program:
```
#include <stdio.h>
#include <string.h>
#include <ctype.h>

char matrix[5][5];

void generateMatrix(char key[]) {
    int used[26] = {0};
    int i, j = 0, k = 0;
    used['J' - 'A'] = 1;

    for (i = 0; key[i] != '\0'; i++) {
        char ch = toupper(key[i]);

        if (ch == 'J')
            ch = 'I';

        if (ch >= 'A' && ch <= 'Z' && !used[ch - 'A']) {
            matrix[j][k++] = ch;
            used[ch - 'A'] = 1;

            if (k == 5) {
                j++;
                k = 0;
            }
        }
    }

    for (i = 0; i < 26; i++) {
        if (!used[i]) {
            matrix[j][k++] = 'A' + i;

            if (k == 5) {
                j++;
                k = 0;
            }
        }
    }
}

void findPosition(char ch, int *row, int *col) {
    if (ch == 'J')
        ch = 'I';

    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            if (matrix[i][j] == ch) {
                *row = i;
                *col = j;
                return;
            }
        }
    }
}

void prepareText(char text[], char prepared[]) {
    int i = 0, k = 0;

    while (text[i] != '\0') {
        char first = toupper(text[i]);

        if (first == ' ') {
            i++;
            continue;
        }

        if (first == 'J')
            first = 'I';

        char second = '\0';

        if (text[i + 1] != '\0') {
            second = toupper(text[i + 1]);

            if (second == 'J')
                second = 'I';
        }

        prepared[k++] = first;

        if (second == '\0') {
            prepared[k++] = 'X';
            i++;
        }
        else if (first == second) {
            prepared[k++] = 'X';
            i++;
        }
        else {
            prepared[k++] = second;
            i += 2;
        }
    }

    if (k % 2 != 0)
        prepared[k++] = 'X';

    prepared[k] = '\0';
}

void encrypt(char text[], char cipher[]) {
    int i, index = 0;

    for (i = 0; text[i] != '\0'; i += 2) {
        int r1, c1, r2, c2;

        findPosition(text[i], &r1, &c1);
        findPosition(text[i + 1], &r2, &c2);

        if (r1 == r2) {
            cipher[index++] = matrix[r1][(c1 + 1) % 5];
            cipher[index++] = matrix[r2][(c2 + 1) % 5];
        }
        else if (c1 == c2) {
            cipher[index++] = matrix[(r1 + 1) % 5][c1];
            cipher[index++] = matrix[(r2 + 1) % 5][c2];
        }
        else {
            cipher[index++] = matrix[r1][c2];
            cipher[index++] = matrix[r2][c1];
        }
    }

    cipher[index] = '\0';
}

void decrypt(char cipher[], char plain[]) {
    int i, index = 0;

    for (i = 0; cipher[i] != '\0'; i += 2) {
        int r1, c1, r2, c2;

        findPosition(cipher[i], &r1, &c1);
        findPosition(cipher[i + 1], &r2, &c2);

        if (r1 == r2) {
            plain[index++] = matrix[r1][(c1 + 4) % 5];
            plain[index++] = matrix[r2][(c2 + 4) % 5];
        }
        else if (c1 == c2) {
            plain[index++] = matrix[(r1 + 4) % 5][c1];
            plain[index++] = matrix[(r2 + 4) % 5][c2];
        }
        else {
            plain[index++] = matrix[r1][c2];
            plain[index++] = matrix[r2][c1];
        }
    }

    plain[index] = '\0';
}

void displayMatrix() {
    printf("\nKey Matrix:\n");

    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++)
            printf("%c ", matrix[i][j]);
        printf("\n");
    }
}

int main() {
    char key[100], plaintext[100];
    char prepared[200], ciphertext[200], decrypted[200];

    printf("Enter plaintext: ");
    scanf("%99s", plaintext);

    printf("Enter key: ");
    scanf("%99s", key);

    generateMatrix(key);
    displayMatrix();

    prepareText(plaintext, prepared);

    printf("\nPrepared Text : %s\n", prepared);

    encrypt(prepared, ciphertext);
    printf("Encrypted Text: %s\n", ciphertext);

    decrypt(ciphertext, decrypted);
    printf("Decrypted Text: %s\n", decrypted);

    return 0;
}
```
## Output:
<img width="1892" height="633" alt="image" src="https://github.com/user-attachments/assets/40d8a713-9533-49db-8d0c-eca0685a2b49" />
## Result:
Successfully the c-program to implemnt Playfair-Cipher.



Output:
