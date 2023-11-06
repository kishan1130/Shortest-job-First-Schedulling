package unit1;

import java.io.*;
import java.text.SimpleDateFormat;
import java.util.*;

class ProcessEntity {
    int processID;
    String processName;
    int executionTime;

    public ProcessEntity(int processID, String processName, int executionTime) {
        this.processID = processID;
        this.processName = processName;
        this.executionTime = executionTime;
    }
}

class CustomPriorityQueue<T> {
    private List<T> data;
    private Comparator<T> comparator;

    public CustomPriorityQueue(Comparator<T> comparator) {
        this.data = new ArrayList<>();
        this.comparator = comparator;
    }

    public void enqueue(T item) {
        data.add(item);
        data.sort(comparator);
    }

    public T dequeue() {
        if (isEmpty()) {
            throw new NoSuchElementException("Priority queue is empty.");
        }
        return data.remove(0); // Remove the element with the highest priority
    }

    public boolean isEmpty() {
        return data.isEmpty();
    }
}

class ProcessExecutionData {
    int processID;
    String processName;
    long startTime;
    long endTime;

    public ProcessExecutionData(int processID, String processName, long startTime) {
        this.processID = processID;
        this.processName = processName;
        this.startTime = startTime;
        this.endTime = -1;  
    }

    public void setEndTime(long endTime) {
        this.endTime = endTime;
    }
}

class LinkedListNode<T> {
    T data;
    LinkedListNode<T> next;

    public LinkedListNode(T data) {
        this.data = data;
        this.next = null;
    }
}

class LinkedList<T> {
    private LinkedListNode<T> head;
    private LinkedListNode<T> tail;

    public void add(T data) {
        LinkedListNode<T> newNode = new LinkedListNode<>(data);
        if (head == null) {
            head = newNode;
            tail = newNode;
        } else {
            tail.next = newNode;
            tail = newNode;
        }
    }

    public void display() {
        LinkedListNode<T> current = head;
        while (current != null) {
            System.out.println(current.data);
            current = current.next;
        }
    }
}

public class SJFSchedulerWithMLAndDataCollection1 {
    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        int n;

        // Use a priority queue to store processes based on execution time (SJF)
        CustomPriorityQueue<ProcessEntity> processQueue = new CustomPriorityQueue<>(Comparator.comparingInt(p -> p.executionTime));
        float avg_wt, avg_tat;

        System.out.println("Enter the number of processes:");
        n = input.nextInt();
        System.out.println("Enter Process Name and Execution Time:");

        for (int i = 0; i < n; i++) {
            System.out.print("Process Name for P" + (i + 1) + ": ");
            String processName = input.next();
            System.out.print("Execution Time for P" + (i + 1) + ": ");
            int executionTime = input.nextInt();
            processQueue.enqueue(new ProcessEntity(i + 1, processName, executionTime));
        }

        int totalWaitingTime = 0;
        int totalTurnaroundTime = 0;
        int waitingTime = 0;

        System.out.println("P\tProcessName\tExecutionTime\tWaitingTime\tTurnaroundTime");

        LinkedList<ProcessExecutionData> executionDataList = new LinkedList<>();

        while (!processQueue.isEmpty()) {
            ProcessEntity currentProcess = processQueue.dequeue();
            int turnaroundTime = waitingTime + currentProcess.executionTime;
            totalWaitingTime += waitingTime;
            totalTurnaroundTime += turnaroundTime;

            System.out.println(currentProcess.processID + "\t" +
                    currentProcess.processName + "\t" +
                    currentProcess.executionTime + "\t" +
                    waitingTime + "\t" +
                    turnaroundTime);

            long startTime = System.currentTimeMillis();
            System.out.println(currentProcess.processName + " started at " + getFormattedTime(startTime));

            try {
                Thread.sleep(currentProcess.executionTime * 1000);  // Simulate execution time in milliseconds
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            long endTime = System.currentTimeMillis();
            System.out.println(currentProcess.processName + " finished at " + getFormattedTime(endTime));

            ProcessExecutionData executionData = new ProcessExecutionData(currentProcess.processID, currentProcess.processName, startTime);
            executionData.setEndTime(endTime);
            executionDataList.add(executionData);

            waitingTime += currentProcess.executionTime;
        }

        avg_wt = (float) totalWaitingTime / n;
        avg_tat = (float) totalTurnaroundTime / n;

        System.out.println("Average Waiting Time= " + avg_wt);
        System.out.println("Average Turnaround Time= " + avg_tat);

        System.out.println("Execution Data:");
        executionDataList.display();
    }

    private static String getFormattedTime(long timestamp) {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date = new Date(timestamp);
        return sdf.format(date);
    }
}
