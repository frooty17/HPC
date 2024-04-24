#include <iostream>
#include <omp.h>

using namespace std;

void merge(int array[], int low, int mid, int high) {
    int temp[30];
    int i, j, k, m;
    j = low;
    m = mid + 1;
    for (i = low; j <= mid && m <= high; i++) {
        if (array[j] <= array[m]) {
            temp[i] = array[j];
            j++;
        } else {
            temp[i] = array[m];
            m++;
        }
    }
    if (j > mid) {
        for (k = m; k <= high; k++) {
            temp[i] = array[k];
            i++;
        }
    } else {
        for (k = j; k <= mid; k++) {
            temp[i] = array[k];
            i++;
        }
    }
    for (k = low; k <= high; k++)
        array[k] = temp[k];
}

void mergesort(int array[], int low, int high) {
    int mid;
    if (low < high) {
        mid = (low + high) / 2;
#pragma omp parallel sections num_threads(2)
        {
#pragma omp section
            {
                mergesort(array, low, mid);
            }
#pragma omp section
            {
                mergesort(array, mid + 1, high);
            }
        }
        merge(array, low, mid, high);
    }
}

int main() {
    int *array, size;
    cout << "Enter total number of elements: ";
    cin >> size;
    array = new int[size];
    cout << "Enter " << size << " elements: ";
    for (int i = 0; i < size; i++) {
        cin >> array[i];
    }

    // Before parallel merge sort
    cout << "\nArray before parallel merge sort: ";
    for (int i = 0; i < size; i++) {
        cout << array[i] << " ";
    }
    cout << endl;

    // Parallel merge sort
    mergesort(array, 0, size - 1);

    // After parallel merge sort
    cout << "\nArray after parallel merge sort: ";
    for (int i = 0; i < size; i++) {
        cout << array[i] << " ";
    }
    cout << endl;

    delete[] array; // Free allocated memory
    return 0;
}
