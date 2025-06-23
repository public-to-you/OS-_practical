- [Page Replacement](#page-replacement)


✨**CPU scheduling**
♦️**FCFS**

#include<stdio.h>
int main(){
    
    int p[10],at[10],bt[10],ct[10],time,n,i;
    float tat[10],wt[10], ttat=0,twt=0;
    int temp;
    
    
    printf("entert number of process");
    scanf("%d",&n);
    
    for(int i=0; i<n; i++){
        p[i]=i+1;
        printf("enter AT & BT of process %d",p[i]);
        scanf("%d%d",&at[i],&bt[i]);
    }
    for (int i=0; i<n-1; i++){
        for(int j=i+1; j<n; j++){
            if(at[i]>at[j]){
                temp=at[i]; at[i]=at[j];at[j]=temp;
                 temp=bt[i]; bt[i]=bt[j];bt[j]=temp;
                  temp=p[i]; p[i]=p[j];p[j]=temp;
            }
        }
    }
    
    time=at[0];
    
     for(int i=0; i<n; i++){
         if(time<at[i]) time=at[i];
         ct[i]=time+bt[i];
         time=ct[i];
         tat[i]=ct[i]-at[i];
         wt[i]=tat[i]-bt[i];
         
         ttat +=tat[i];
         twt += wt[i];
     }
     
    printf("\np\tAT\tBT\tCT\tTAT\tWT");
    for(int i=0; i<n; i++)
     {
         printf("P%d\t%d\t%d\t%d\t%.1f\t%.1f\n",p[i],at[i],bt[i],ct[i],tat[i],wt[i]);
     }    
     printf("Gantt chart");
     for(int i=0; i<n; i++) {
         printf("P%d  |",p[i]);
     }
     printf("\n%d",at[0]);
    for( i=0;i<n;i++){
         printf("  %d",ct[i]);
    }
     
     printf("Total TAT: %.2f\n Total WT: %.2f", ttat/n,twt/n);
    
    
    return 0;
}

♦️**sjf**
**nonpreemptive**


#include <stdio.h>

int main() {
    int n, i, j;
    int pid[20], at[20], bt[20], ct[20], tat[20], wt[20], done[20] = {0};
    int t = 0, cnt = 0;
    float sumWT = 0, sumTAT = 0;

    // To store Gantt chart sequence
    int ganttPID[20], ganttTime[20], g = 0;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    for (i = 0; i < n; i++) {
        pid[i] = i + 1;
        printf("Enter AT & BT for P%d: ", pid[i]);
        scanf("%d%d", &at[i], &bt[i]);
    }

    printf("\nGantt Chart:\n|");

    while (cnt < n) {
        int found = 0;
        for (i = 0; i < n; i++) {
            if (!done[i] && at[i] <= t) {
                found = 1;
                for (j = 0; j < n; j++) {
                    if (!done[j] && at[j] <= t && bt[j] < bt[i])
                        break;
                }
                if (j == n) {
                    printf(" P%d |", pid[i]);
                    ganttPID[g] = pid[i];
                    t += bt[i];
                    ganttTime[g++] = t;

                    ct[i] = t;
                    tat[i] = ct[i] - at[i];
                    wt[i] = tat[i] - bt[i];
                    done[i] = 1;
                    sumWT += wt[i];
                    sumTAT += tat[i];
                    cnt++;
                    break;
                }
            }
        }
        if (!found) {
            t++;
        }
    }

    // Print time line
    printf("\n0");
    for (i = 0; i < g; i++) {
        printf("  %d", ganttTime[i]);
    }

    // Process Table
    printf("\n\nPID\tAT\tBT\tWT\tTAT");
    for (i = 0; i < n; i++) {
        printf("\nP%d\t%d\t%d\t%d\t%d", pid[i], at[i], bt[i], wt[i], tat[i]);
    }

    printf("\n\nAvg WT = %.2f", sumWT / n);
    printf("\nAvg TAT = %.2f\n", sumTAT / n);

    return 0;
}


♦️**priority-scheduling**
♦️***SJF Preemptive (SRTF)****
#include <stdio.h>

int main() {
    int n, i;
    int at[20], bt[20], rt[20], ct[20], tat[20], wt[20];
    int complete = 0, t = 0, shortest, finish_time;
    int last_pid = -1;
    float sumWT = 0, sumTAT = 0;

    // For Gantt Chart
    int gantt_pid[100], gantt_time[100], g = 0;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    for(i = 0; i < n; i++) {
        printf("Enter Arrival Time and Burst Time for P%d: ", i+1);
        scanf("%d%d", &at[i], &bt[i]);
        rt[i] = bt[i];  // Initialize remaining time
    }

    while(complete != n) {
        shortest = -1;

        for(i = 0; i < n; i++) {
            if(at[i] <= t && rt[i] > 0) {
                if(shortest == -1 || rt[i] < rt[shortest])
                    shortest = i;
            }
        }

        if(shortest == -1) {
            t++;
            continue;
        }

        rt[shortest]--;

        // Track process change for Gantt chart
        if(last_pid != shortest) {
            gantt_pid[g] = shortest;
            gantt_time[g++] = t;
            last_pid = shortest;
        }

        if(rt[shortest] == 0) {
            complete++;
            ct[shortest] = t + 1;
            tat[shortest] = ct[shortest] - at[shortest];
            wt[shortest] = tat[shortest] - bt[shortest];
            sumWT += wt[shortest];
            sumTAT += tat[shortest];
        }

        t++;
    }

    // Last time for Gantt chart
    gantt_time[g] = t;

    // Print Gantt Chart
    printf("\n\nGANTT CHART:\n|");
    for(i = 0; i < g; i++) {
        printf(" P%d |", gantt_pid[i]+1);
    }

    printf("\n%d", gantt_time[0]);
    for(i = 1; i <= g; i++) {
        printf("   %d", gantt_time[i]);
    }

    // Print Process Table
    printf("\n\nPROCESS TABLE:");
    printf("\nPID\tAT\tBT\tCT\tTAT\tWT");
    for(i = 0; i < n; i++) {
        printf("\nP%d\t%d\t%d\t%d\t%d\t%d", i+1, at[i], bt[i], ct[i], tat[i], wt[i]);
    }

    printf("\n\nAverage Waiting Time: %.2f", sumWT / n);
    printf("\nAverage Turnaround Time: %.2f\n", sumTAT / n);

    return 0;
}


*♦️*****Preemptive Priority Scheduling******

#include <stdio.h>

int main() {
    int n, i;
    int at[20], bt[20], pr[20], rt[20], ct[20], tat[20], wt[20];
    int complete = 0, t = 0, highest = -1, last_pid = -1;
    float sumWT = 0, sumTAT = 0;

    // For Gantt Chart
    int gantt_pid[100], gantt_time[100], g = 0;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    for(i = 0; i < n; i++) {
        printf("Enter AT, BT and Priority for P%d: ", i+1);
        scanf("%d%d%d", &at[i], &bt[i], &pr[i]);
        rt[i] = bt[i]; // Initialize remaining time
    }

    while (complete != n) {
        highest = -1;
        for(i = 0; i < n; i++) {
            if(at[i] <= t && rt[i] > 0) {
                if(highest == -1 || pr[i] < pr[highest]) { // Lower value => Higher priority
                    highest = i;
                }
            }
        }

        if(highest == -1) {
            t++;
            continue;
        }

        rt[highest]--;

        // Log Gantt chart
        if(last_pid != highest) {
            gantt_pid[g] = highest;
            gantt_time[g++] = t;
            last_pid = highest;
        }

        if(rt[highest] == 0) {
            complete++;
            ct[highest] = t + 1;
            tat[highest] = ct[highest] - at[highest];
            wt[highest] = tat[highest] - bt[highest];
            sumWT += wt[highest];
            sumTAT += tat[highest];
        }

        t++;
    }

    // Add final time to Gantt chart
    gantt_time[g] = t;

    // Gantt Chart
    printf("\n\nGANTT CHART:\n|");
    for(i = 0; i < g; i++) {
        printf(" P%d |", gantt_pid[i]+1);
    }
    printf("\n%d", gantt_time[0]);
    for(i = 1; i <= g; i++) {
        printf("   %d", gantt_time[i]);
    }

    // Process Table
    printf("\n\nPROCESS TABLE:\nPID\tAT\tBT\tPR\tCT\tTAT\tWT");
    for(i = 0; i < n; i++) {
        printf("\nP%d\t%d\t%d\t%d\t%d\t%d\t%d", i+1, at[i], bt[i], pr[i], ct[i], tat[i], wt[i]);
    }

    printf("\n\nAverage Waiting Time: %.2f", sumWT / n);
    printf("\nAverage Turnaround Time: %.2f\n", sumTAT / n);

    return 0;
}


♦️♦️******Priority Scheduling (Non-Preemptive)******

#include <stdio.h>

int main() {
    int n, i, j;
    int pid[20], at[20], bt[20], pr[20], ct[20], tat[20], wt[20], done[20] = {0};
    int t = 0, completed = 0;
    float totalWT = 0, totalTAT = 0;

    // Gantt Chart arrays
    int gantt_pid[100], gantt_time[100], g = 0;

    printf("Enter number of processes: ");
    scanf("%d", &n);

    for(i = 0; i < n; i++) {
        pid[i] = i + 1;
        printf("Enter AT, BT and Priority for P%d: ", pid[i]);
        scanf("%d%d%d", &at[i], &bt[i], &pr[i]);
    }

    while(completed < n) {
        int idx = -1;
        int highest_priority = 9999;

        for(i = 0; i < n; i++) {
            if(at[i] <= t && !done[i]) {
                if(pr[i] < highest_priority) {
                    highest_priority = pr[i];
                    idx = i;
                } else if(pr[i] == highest_priority) {
                    // Tie breaker: earlier arrival time
                    if(at[i] < at[idx]) {
                        idx = i;
                    }
                }
            }
        }

        if(idx != -1) {
            wt[idx] = t - at[idx];
            if(wt[idx] < 0) wt[idx] = 0;
            ct[idx] = t + bt[idx];
            tat[idx] = ct[idx] - at[idx];
            done[idx] = 1;
            totalWT += wt[idx];
            totalTAT += tat[idx];
            t = ct[idx];
            completed++;

            // Gantt chart
            gantt_pid[g] = pid[idx];
            gantt_time[g++] = t;
        } else {
            t++; // CPU idle
        }
    }

    // Print Gantt Chart
    printf("\n\nGANTT CHART:\n|");
    int prev_time = 0;
    for(i = 0; i < g; i++) {
        printf(" P%d |", gantt_pid[i]);
    }

    printf("\n0");
    for(i = 0; i < g; i++) {
        printf("   %d", gantt_time[i]);
    }

    // Print Table
    printf("\n\nPROCESS TABLE:\nPID\tAT\tBT\tPR\tCT\tTAT\tWT");
    for(i = 0; i < n; i++) {
        printf("\nP%d\t%d\t%d\t%d\t%d\t%d\t%d", pid[i], at[i], bt[i], pr[i], ct[i], tat[i], wt[i]);
    }

    printf("\n\nAverage Waiting Time: %.2f", totalWT / n);
    printf("\nAverage Turnaround Time: %.2f\n", totalTAT / n);

    return 0;
}




♦️****Round Robin******

#include <stdio.h>

int main() { int n, i, time = 0, tq; int at[20], bt[20], rt[20], wt[20], tat[20], ct[20], pid[20]; int completed = 0; float total_wt = 0, total_tat = 0;

printf("Enter number of processes: "); 
scanf("%d", &n); 

for(i = 0; i < n; i++) { 
    pid[i] = i + 1; 
    printf("Enter Arrival Time for Process P%d: ", pid[i]); 
    scanf("%d", &at[i]); 
    printf("Enter Burst Time for Process P%d: ", pid[i]); 
    scanf("%d", &bt[i]); 
    rt[i] = bt[i]; // remaining time 
} 

printf("Enter Time Quantum: "); 
scanf("%d", &tq); 

int queue[100], front = 0, rear = 0; 
int visited[20] = {0}; 
queue[rear++] = 0; // enqueue first process 
visited[0] = 1; 

while(completed < n) { 
    int idx = queue[front++]; 
    
    if(rt[idx] > tq) { 
        time += tq; 
        rt[idx] -= tq; 
    } else { 
        time += rt[idx]; 
        rt[idx] = 0; 
        completed++; 
        ct[idx] = time; 
        tat[idx] = ct[idx] - at[idx]; 
        wt[idx] = tat[idx] - bt[idx]; 
        total_wt += wt[idx]; 
        total_tat += tat[idx]; 
    } 

    // Enqueue newly arrived processes 
    for(i = 0; i < n; i++) { 
        if(i != idx && at[i] <= time && rt[i] > 0 && !visited[i]) { 
            queue[rear++] = i; 
            visited[i] = 1; 
        } 
    } 

    // Re-enqueue current process if not finished 
    if(rt[idx] > 0) { 
        queue[rear++] = idx; 
    } 

    // If queue is empty, push the next process with remaining time 
    if(front == rear) { 
        for(i = 0; i < n; i++) { 
            if(rt[i] > 0) { 
                queue[rear++] = i; 
                visited[i] = 1; 
                break; 
            } 
        } 
    } 
} 

printf("\nProcess\tAT\tBT\tCT\tWT\tTAT\n"); 
for(i = 0; i < n; i++) { 
    printf("P%d\t%d\t%d\t%d\t%d\t%d\n", pid[i], at[i], bt[i], ct[i], 

wt[i], tat[i]); }

printf("\nAverage Waiting Time = %.2f", total_wt / n); 
printf("\nAverage Turnaround Time = %.2f\n", total_tat / n); 

return 0; 

}

## Page Replacement

♦️****Page replacement****
****FIFO****

#include <stdio.h>

int main() {
    int ref[100], frames[10][100], current[10];
    int n, f, i, j, pos = 0, faults = 0, hits = 0;

    printf("Enter length of reference string: ");
    scanf("%d", &n);

    printf("Enter %d page numbers (reference string):\n", n);
    for (i = 0; i < n; i++) scanf("%d", &ref[i]);

    printf("Enter number of frames: ");
    scanf("%d", &f);

    for (i = 0; i < f; i++) current[i] = -1;

    // FIFO Page Replacement Logic
    for (i = 0; i < n; i++) {
        int found = 0;
        for (j = 0; j < f; j++) {
            if (current[j] == ref[i]) {
                found = 1;
                hits++;
                break;
            }
        }

        if (!found) {
            current[pos] = ref[i];
            pos = (pos + 1) % f;
            faults++;
        }

        for (j = 0; j < f; j++)
            frames[j][i] = current[j];
    }

    // Print the reference string
    printf("\nPages:\n");
  

    // Print the frame table with F3 (top) to F1 (bottom)
    printf("\nPage Frame Table\n");
    for (i = f - 1; i >= 0; i--)
 {
        printf("F%d |", i + 1);
        for (j = 0; j < n; j++)
 {
            if (frames[i][j] == -1)
                printf(" -- ");
            else
                printf(" %2d ", frames[i][j]);
        }
        printf("\n");
    }

    // Print statistics
    printf("\nTotal Page Faults = %d", faults);
    printf("\nTotal Page Hits   = %d", hits);
    printf("\nPage Fault Ratio  = %.2f%%", (faults * 100.0) / n);
    printf("\nPage Hit Ratio    = %.2f%%\n", (hits * 100.0) / n);

    return 0;
}



###**Optimal:******

#include <stdio.h>

int findOptimal(int pages[], int frames[], int n, int f, int index) { int farthest = index, pos = -1, i, j; for(i = 0; i < f; i++) { int found = 0; for(j = index; j < n; j++) { if(frames[i] == pages[j]) { if(j > farthest) { farthest = j; pos = i; } found = 1; break; } } if(!found) return i; // Page not used again } return (pos == -1) ? 0 : pos; }

int main() { int pages[100], frames[10], n, f, i, j, faults = 0, hit = 0; int found;

printf("Enter number of pages: "); 
scanf("%d", &n); 

printf("Enter the page reference string:\n"); 
for(i = 0; i < n; i++) { 
    scanf("%d", &pages[i]); 
} 

printf("Enter number of frames: "); 
scanf("%d", &f); 

for(i = 0; i < f; i++) 
    frames[i] = -1; 

printf("\nPage\tFrames\t\tPage Fault\n"); 

for(i = 0; i < n; i++) { 
    found = 0; 

    for(j = 0; j < f; j++) { 
        if(frames[j] == pages[i]) { 
            found = 1; 
            hit++; 
            break; 
        } 
    } 

    if(!found) { 
        int pos = -1; 
        for(j = 0; j < f; j++) { 
            if(frames[j] == -1) { 
                pos = j; 
                break; 
            } 
        } 
        if(pos == -1) 
            pos = findOptimal(pages, frames, n, f, i+1); 

        frames[pos] = pages[i]; 
        faults++; 
    } 

    printf("%d\t", pages[i]); 
    for(j = 0; j < f; j++) { 
        if(frames[j] != -1) 
            printf("%d ", frames[j]); 
        else 
            printf("- "); 
    } 
    printf("\t%s\n", found ? "Hit" : "Miss"); 
} 

printf("\nTotal Page Faults: %d", faults); 
printf("\nTotal Page Hits: %d\n", hit); 

return 0; 

}





####**LRu******

#include <stdio.h>

int findLRU(int time[], int f) { int min = time[0], pos = 0; for(int i = 1; i < f; i++) { if(time[i] < min) { min = time[i]; pos = i; } } return pos; }

int main() { int pages[100], frames[10], time[10], n, f; int i, j, k, pos, faults = 0, counter = 0, hit = 0; int found;

printf("Enter number of pages: "); 
scanf("%d", &n); 

printf("Enter the page reference string:\n"); 
for(i = 0; i < n; i++) { 
    scanf("%d", &pages[i]); 
} 

printf("Enter number of frames: "); 
scanf("%d", &f); 

for(i = 0; i < f; i++) { 
    frames[i] = -1; 
    time[i] = 0; 
} 

printf("\nPage\tFrames\t\tPage Fault\n"); 

for(i = 0; i < n; i++) { 
    found = 0; 

    for(j = 0; j < f; j++) { 
        if(frames[j] == pages[i]) { 
            counter++; 
            time[j] = counter; 
            found = 1; 
            hit++; 
            break; 
        } 
    } 

    if(!found) { 
        for(j = 0; j < f; j++) { 
            if(frames[j] == -1) { 
                counter++; 
                frames[j] = pages[i]; 
                time[j] = counter; 
                faults++; 
                found = 1; 
                break; 
            } 
        } 
    } 

    if(!found) { 
        pos = findLRU(time, f); 
        counter++; 
        frames[pos] = pages[i]; 
        time[pos] = counter; 
        faults++; 
    } 

    printf("%d\t", pages[i]); 
    for(j = 0; j < f; j++) { 
        if(frames[j] != -1) 
            printf("%d ", frames[j]); 
        else 
            printf("- "); 
    } 
    printf("\t%s\n", found ? "Hit" : "Miss"); 
} 

printf("\nTotal Page Faults: %d", faults); 
printf("\nTotal Page Hits: %d\n", hit); 

return 0; 

}

###****Bankers****
#include<stdio.h>
int main(){
    
    
    int i,j,k;
    int n=5,m=3,safes[10],finish[10]={0},avail[3]={3,3,2},need[5][3],count=0,safe[10];
     // Allocation Matrix
    int allc[5][3] = {
        {0, 1, 0},  // P0
        {2, 0, 0},  // P1
        {3, 0, 2},  // P2
        {2, 1, 1},  // P3
        {0, 0, 2}   // P4
    };

    // Max Matrix
    int max[5][3] = {
        {7, 5, 3},  // P0
        {3, 2, 2},  // P1
        {9, 0, 2},  // P2
        {2, 2, 2},  // P3
        {4, 3, 3}   // P4
    };
    
    for(i=0;i<n;i++){
        for(j=0;j<m;j++){
            need[i][j]=max[i][j]-allc[i][j];
        }
    }
    
    while(count<n){
        int found=0;
        for(i=0;i<n;i++){
            if(!finish[i]){
                int canrun=1;
                for(j=0;j<m;j++){
                    if(need[i][j]>avail[j])
                    {
                        canrun=0;
                        break;
                    }
                }
                if(canrun)
                {
                    for(k=0;k<m;k++){
                            avail[k]+=allc[i][k];
                        
                    }
                    safe[count++]=i;
                    found=1;
                    finish[i]=1;
                }
            }
                  
        }
         if(!found){
                printf("notfound");
            }
        
    }
    printf("safe");
    for(i=0;i<n;i++){
        printf("P%d >",safe[i]);
    }
    
    
    return 0;
}


###**First fit****
#include <stdio.h>

int main() {
    int i, j, nb, np;
    int block[10], process[10], allocation[10];

    printf("Enter number of blocks: ");
    scanf("%d", &nb);
    printf("Enter block sizes:\n");
    for(i = 0; i < nb; i++)
        scanf("%d", &block[i]);

    printf("Enter number of processes: ");
    scanf("%d", &np);
    printf("Enter process sizes:\n");
    for(i = 0; i < np; i++) {
        scanf("%d", &process[i]);
        allocation[i] = -1;
    }

    for(i = 0; i < np; i++) {
        for(j = 0; j < nb; j++) {
            if(block[j] >= process[i]) {
                allocation[i] = j;
                block[j] = block[j]-process[i];
                break;
            }
        }
    }

    printf("\nProcess No\tSize\tBlock No\n");
    for(i = 0; i < np; i++) {
        printf("%d\t\t%d\t", i+1, process[i]);
        if(allocation[i] != -1)
            printf("%d\n", allocation[i]+1);
        else
            printf("Not Allocated\n");
    }

    return 0;
}


###**worst fit**

#include <stdio.h>

int main() {
    int nb, np, bl[10], p[10], allocation[10], i, j;

    printf("Enter number of blocks: ");
    scanf("%d", &nb);
    printf("Enter sizes of %d blocks: ", nb);
    for (i = 0; i < nb; i++)
        scanf("%d", &bl[i]);

    printf("Enter number of processes: ");
    scanf("%d", &np);
    printf("Enter sizes of %d processes: ", np);
    for (i = 0; i < np; i++) {
        scanf("%d", &p[i]);
        allocation[i] = -1;  // initialize as not allocated
    }

    // Worst Fit Logic
    for (i = 0; i < np; i++) {
        int worstIdx = -1;
        for (j = 0; j < nb; j++) {
            if (bl[j] >= p[i]) {
                if (worstIdx == -1 || bl[j] > bl[worstIdx])
                    worstIdx = j;
            }
        }

        if (worstIdx != -1) {
            allocation[i] = worstIdx;
            bl[worstIdx] -= p[i];
        }
    }

    // Display Allocation Table
    printf("\nProcess\tSize\tBlock\n");
    for (i = 0; i < np; i++) {
        printf("P%d\t%d\t", i + 1, p[i]);
        if (allocation[i] != -1)
            printf("%d\n", allocation[i] + 1);  // +1 to show block number starting from 1
        else
            printf("Not Allocated\n");
    }

    return 0;
}


###**Best fit******

#include <stdio.h>

int main() {
    int nb, np, bl[10], p[10], allocation[10], i, j;

    printf("Enter number of blocks: ");
    scanf("%d", &nb);
    printf("Enter sizes of %d blocks: ", nb);
    for (i = 0; i < nb; i++)
        scanf("%d", &bl[i]);

    printf("Enter number of processes: ");
    scanf("%d", &np);
    printf("Enter sizes of %d processes: ", np);
    for (i = 0; i < np; i++) {
        scanf("%d", &p[i]);
        allocation[i] = -1;  // initially unallocated
    }

    // Best Fit Logic
    for (i = 0; i < np; i++) {
        int bestIdx = -1;
        for (j = 0; j < nb; j++) {
            if (bl[j] >= p[i]) {
                if (bestIdx == -1 || bl[j] < bl[bestIdx])
                    bestIdx = j;
            }
        }

        if (bestIdx != -1) {
            allocation[i] = bestIdx;
            bl[bestIdx] -= p[i];
        }
    }

    // Output
    printf("\nProcess\tSize\tBlock\n");
    for (i = 0; i < np; i++) {
        printf("P%d\t%d\t", i + 1, p[i]);
        if (allocation[i] != -1)
            printf("%d\n", allocation[i] + 1);  // block number (1-indexed)
        else
            printf("Not Allocated\n");
    }

    return 0;
}





###**File allocation******

#include <stdio.h> #include <stdlib.h>

#define MAX 50

int memory[MAX]; // 0 = free, 1 = allocated

// Function to reset memory void resetMemory() { for (int i = 0; i < MAX; i++) { memory[i] = 0; } }

// Sequential Allocation void sequentialAllocation() { int start, length, i, flag = 0;

printf("\n--- Sequential Allocation ---\n"); 
printf("Enter starting block and length of file: "); 
scanf("%d %d", &start, &length); 

if (start + length > MAX) { 
    printf("Memory exceeds limit!\n"); 
    return; 
} 

for (i = start; i < start + length; i++) { 
    if (memory[i] == 1) { 
        flag = 1; 
        break; 
    } 
} 

if (flag == 0) { 
    for (i = start; i < start + length; i++) { 
        memory[i] = 1; 
    } 
    printf("File allocated successfully in blocks: "); 
    for (i = start; i < start + length; i++) { 
        printf("%d ", i); 
    } 
    printf("\n"); 
} else { 
    printf("Blocks already allocated. Try different blocks.\n"); 
} 

}

// Indexed Allocation void indexedAllocation() { int index, n, i, block;

printf("\n--- Indexed Allocation ---\n"); 
printf("Enter index block: "); 
scanf("%d", &index); 

if (memory[index] == 1) { 
    printf("Index block already allocated.\n"); 
    return; 
} 

printf("Enter number of blocks needed: "); 
scanf("%d", &n); 

int dataBlocks[n]; 

printf("Enter block numbers:\n"); 
for (i = 0; i < n; i++) { 
    scanf("%d", &block); 
    if (memory[block] == 1 || block == index) { 
        printf("Block %d already allocated or invalid. Try again.\n", 

block); return; } dataBlocks[i] = block; }

memory[index] = 1; 
for (i = 0; i < n; i++) { 
    memory[dataBlocks[i]] = 1; 
} 

printf("File indexed at block %d and allocated blocks: ", index); 
for (i = 0; i < n; i++) { 
    printf("%d ", dataBlocks[i]); 
} 
printf("\n"); 

}

// Linked Allocation void linkedAllocation() { int n, i, block; int list[MAX], count = 0;

printf("\n--- Linked Allocation ---\n"); 
printf("Enter number of blocks for the file: "); 
scanf("%d", &n); 

printf("Enter %d free block numbers:\n", n); 
for (i = 0; i < n; i++) { 
    scanf("%d", &block); 
    if (memory[block] == 1 || block >= MAX) { 
        printf("Block %d is already allocated or invalid.\n", block); 
        return; 
    } 
    list[count++] = block; 
} 

for (i = 0; i < count; i++) { 
    memory[list[i]] = 1; 
} 

printf("Linked file allocated at blocks: "); 
for (i = 0; i < count; i++) { 
    printf("%d -> ", list[i]); 
} 
printf("NULL\n"); 

}

// Menu int main() { int choice;

while (1) { 
    printf("\n\n--- File Allocation Menu ---\n"); 
    printf("1. Sequential Allocation\n"); 
    printf("2. Indexed Allocation\n"); 
    printf("3. Linked Allocation\n"); 
    printf("4. Reset Memory\n"); 
    printf("5. Exit\n"); 
    printf("Enter your choice: "); 
    scanf("%d", &choice); 

    switch (choice) { 
        case 1: 
            sequentialAllocation(); 
            break; 
        case 2: 
            indexedAllocation(); 
            break; 
        case 3: 
            linkedAllocation(); 
            break; 
        case 4: 
            resetMemory(); 
            printf("Memory reset successful.\n"); 
            break; 
        case 5: 
            exit(0); 
        default: 
            printf("Invalid choice.\n"); 
    } 
} 

return 0; 

}



###**Semaphore**

#include <stdio.h> #include <pthread.h> #include <semaphore.h> #include <unistd.h>

int buffer; // Only 1 item space int count = 0; // Buffer size (0 or 1)

sem_t empty; // Tells if buffer is empty sem_t full; // Tells if buffer has data

void* producer() { for (int i = 0; i < 5; i++) { sem_wait(&empty); // Wait if buffer is not empty

    buffer = i;        // Produce item 
    count = 1; 
    printf("Produced: %d | Buffer size: %d\n", i, count); 

    sem_post(&full);   // Say buffer is full now 
    sleep(1);          // Wait for 1 second 
} 
return NULL; 

}

void* consumer() { for (int i = 0; i < 5; i++) { sem_wait(&full); // Wait if buffer is empty

    int item = buffer; // Consume item 
    count = 0; 
    printf("Consumed: %d | Buffer size: %d\n", item, count); 

    sem_post(&empty);  // Say buffer is empty now 
    sleep(1);          // Wait for 1 second 
} 
return NULL; 

}

int main() { pthread_t p, c;

sem_init(&empty, 0, 1);  // Buffer starts empty 
sem_init(&full, 0, 0);   // Nothing to consume 

pthread_create(&p, NULL, producer, NULL);  // Start producer 
pthread_create(&c, NULL, consumer, NULL);  // Start consumer 

pthread_join(p, NULL);  // Wait for    r to finish 
pthread_join(c, NULL);  // Wait for consumer to finish 

sem_destroy(&empty); 
sem_destroy(&full); 

return 0; 

}
