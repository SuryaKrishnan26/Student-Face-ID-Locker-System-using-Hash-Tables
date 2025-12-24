#include <stdio.h>
#include <stdlib.h>
#include <opencv2/opencv.hpp>

#define SIZE 10

using namespace cv;

struct Student {
    int studentID;
    int occupied;
};

struct Student hashTable[SIZE];

int hashFunction(int key) {
    return key % SIZE;
}

void insertStudent(int studentID) {
    int index = hashFunction(studentID);
    while (hashTable[index].occupied) {
        index = (index + 1) % SIZE;
    }
    hashTable[index].studentID = studentID;
    hashTable[index].occupied = 1;
}

void authenticateStudent() {
    VideoCapture cap(0);
    if (!cap.isOpened()) {
        printf("Camera access failed\n");
        return;
    }

    Mat frame;
    printf("Press 'c' to capture face\n");

    while (1) {
        cap >> frame;
        imshow("Face ID Locker", frame);

        char key = waitKey(1);
        if (key == 'c') break;
        if (key == 27) {
            cap.release();
            destroyAllWindows();
            return;
        }
    }

    int id;
    printf("Enter Student ID: ");
    scanf("%d", &id);

    int index = hashFunction(id);
    for (int i = 0; i < SIZE; i++) {
        if (hashTable[index].occupied &&
            hashTable[index].studentID == id) {
            printf("Access Granted\n");
            cap.release();
            destroyAllWindows();
            return;
        }
        index = (index + 1) % SIZE;
    }

    printf("Access Denied\n");
    cap.release();
    destroyAllWindows();
}

void display() {
    for (int i = 0; i < SIZE; i++) {
        if (hashTable[i].occupied)
            printf("Index %d -> Student ID: %d\n", i, hashTable[i].studentID);
        else
            printf("Index %d -> Empty\n", i);
    }
}

int main() {
    int choice, id;

    for (int i = 0; i < SIZE; i++)
        hashTable[i].occupied = 0;

    while (1) {
        printf("\n1.Register\n2.Authenticate(Camera)\n3.Display\n4.Exit\n");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                printf("Enter Student ID: ");
                scanf("%d", &id);
                insertStudent(id);
                break;
            case 2:
                authenticateStudent();
                break;
            case 3:
                display();
                break;
            case 4:
                exit(0);
        }
    }
    return 0;
}
