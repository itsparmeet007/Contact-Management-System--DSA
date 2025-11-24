#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX 100

// ---------------- STRUCTURES ----------------

typedef struct Contact {
    char name[50];
    char phone[20];
    char email[50];
    struct Contact *next;
} Contact;

typedef struct Stack {
    Contact *data[MAX];
    int top;
} Stack;

// ---------------- GLOBAL VARIABLES ----------------

Contact *head = NULL;
Stack undoStack;
int contactCount = 0;

// ---------------- FUNCTION DECLARATIONS ----------------

void addContact();
void displayContacts();
void searchContact();
void deleteContact();
void sortContacts();
void undoLastAdd();
void saveToFile();
void loadFromFile();
void initSystem();
void menu();

// ---------------- STACK FUNCTIONS ----------------

void initStack(Stack *s) {
    s->top = -1;
}

void push(Stack *s, Contact *c) {
    if (s->top == MAX - 1) {
        printf("\n⚠ Stack full! Cannot push.\n");
        return;
    }
    s->data[++s->top] = c;
}

Contact* pop(Stack *s) {
    if (s->top == -1) return NULL;
    return s->data[s->top--];
}

// ---------------- LINKED LIST FUNCTIONS ----------------

Contact* createContact(char name[], char phone[], char email[]) {
    Contact *newC = (Contact *)malloc(sizeof(Contact));
    if (!newC) {
        printf("Memory allocation failed!\n");
        exit(1);
    }
    strcpy(newC->name, name);
    strcpy(newC->phone, phone);
    strcpy(newC->email, email);
    newC->next = NULL;
    return newC;
}

void addContact() {
    char name[50], phone[20], email[50];

    getchar();
    printf("\nEnter Name: ");
    fgets(name, 50, stdin);
    name[strcspn(name, "\n")] = 0;

    printf("Enter Phone: ");
    fgets(phone, 20, stdin);
    phone[strcspn(phone, "\n")] = 0;

    printf("Enter Email: ");
    fgets(email, 50, stdin);
    email[strcspn(email, "\n")] = 0;

    Contact *newC = createContact(name, phone, email);

    if (head == NULL)
        head = newC;
    else {
        Contact *temp = head;
        while (temp->next != NULL)
            temp = temp->next;
        temp->next = newC;
    }

    push(&undoStack, newC);
    contactCount++;
    printf("\n✅ Contact added successfully.\n");
}

void displayContacts() {
    if (head == NULL) {
        printf("\nNo contacts found.\n");
        return;
    }

    printf("\n----- CONTACT LIST -----\n");
    printf("%-20s %-15s %-30s\n", "Name", "Phone", "Email");
    printf("---------------------------------------------------------------\n");

    Contact *temp = head;
    while (temp != NULL) {
        printf("%-20s %-15s %-30s\n", temp->name, temp->phone, temp->email);
        temp = temp->next;
    }
}

void searchContact() {
    char searchName[50];
    getchar();
    printf("\nEnter name to search: ");
    fgets(searchName, 50, stdin);
    searchName[strcspn(searchName, "\n")] = 0;

    Contact *temp = head;
    while (temp != NULL) {
        if (strcasecmp(temp->name, searchName) == 0) {
            printf("\nRecord Found:\n");
            printf("Name: %s\nPhone: %s\nEmail: %s\n", temp->name, temp->phone, temp->email);
            return;
        }
        temp = temp->next;
    }
    printf("\n❌ Contact not found.\n");
}

void deleteContact() {
    char name[50];
    getchar();
    printf("\nEnter name to delete: ");
    fgets(name, 50, stdin);
    name[strcspn(name, "\n")] = 0;

    Contact *temp = head, *prev = NULL;

    while (temp != NULL && strcasecmp(temp->name, name) != 0) {
        prev = temp;
        temp = temp->next;
    }

    if (temp == NULL) {
        printf("\n❌ Contact not found.\n");
        return;
    }

    if (prev == NULL)
        head = temp->next;
    else
        prev->next = temp->next;

    free(temp);
    contactCount--;
    printf("\n✅ Contact deleted successfully.\n");
}

void sortContacts() {
    if (head == NULL) {
        printf("\nNo contacts to sort.\n");
        return;
    }

    Contact *i, *j;
    char tempName[50], tempPhone[20], tempEmail[50];

    for (i = head; i != NULL; i = i->next) {
        for (j = i->next; j != NULL; j = j->next) {
            if (strcasecmp(i->name, j->name) > 0) {
                strcpy(tempName, i->name);
                strcpy(tempPhone, i->phone);
                strcpy(tempEmail, i->email);

                strcpy(i->name, j->name);
                strcpy(i->phone, j->phone);
                strcpy(i->email, j->email);

                strcpy(j->name, tempName);
                strcpy(j->phone, tempPhone);
                strcpy(j->email, tempEmail);
            }
        }
    }
    printf("\n✅ Contacts sorted alphabetically by name.\n");
}

void undoLastAdd() {
    Contact *c = pop(&undoStack);
    if (c == NULL) {
        printf("\nNothing to undo.\n");
        return;
    }

    Contact *temp = head, *prev = NULL;
    while (temp != NULL && temp != c) {
        prev = temp;
        temp = temp->next;
    }

    if (temp == NULL) return;

    if (prev == NULL)
        head = temp->next;
    else
        prev->next = temp->next;

    free(temp);
    contactCount--;
    printf("\n↩  Last addition undone successfully.\n");
}

// ---------------- FILE HANDLING ----------------

void saveToFile() {
    FILE *fp = fopen("contacts.txt", "w");
    if (!fp) {
        printf("\n⚠ Error saving to file.\n");
        return;
    }

    Contact *temp = head;
    while (temp != NULL) {
        fprintf(fp, "%s,%s,%s\n", temp->name, temp->phone, temp->email);
        temp = temp->next;
    }
    fclose(fp);
}

void loadFromFile() {
    FILE *fp = fopen("contacts.txt", "r");
    if (!fp) return;

    char name[50], phone[20], email[50];
    while (fscanf(fp, "%49[^,],%19[^,],%49[^\n]\n", name, phone, email) == 3) {
        Contact *newC = createContact(name, phone, email);
        if (head == NULL)
            head = newC;
        else {
            Contact *temp = head;
            while (temp->next != NULL)
                temp = temp->next;
            temp->next = newC;
        }
        contactCount++;
    }
    fclose(fp);
}

// ---------------- INITIALIZATION ----------------

void initSystem() {
    initStack(&undoStack);
    loadFromFile();
}

// ---------------- MENU ----------------

void menu() {
    int choice;
    initSystem();

    while (1) {
        printf("\n===== CONTACT MANAGEMENT SYSTEM =====\n");
        printf("1. Add Contact\n");
        printf("2. Display All Contacts\n");
        printf("3. Search Contact\n");
        printf("4. Delete Contact\n");
        printf("5. Sort Contacts by Name\n");
        printf("6. Undo Last Addition (Stack)\n");
        printf("7. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: addContact(); break;
            case 2: displayContacts(); break;
            case 3: searchContact(); break;
            case 4: deleteContact(); break;
            case 5: sortContacts(); break;
            case 6: undoLastAdd(); break;
            case 7:
                saveToFile();
                printf("\n💾 Data saved successfully. Exiting...\n");
                exit(0);
            default:
                printf("Invalid choice! Try again.\n");
        }
    }
}

// ---------------- MAIN ----------------

int main() {
    menu();
    return 0;
}
