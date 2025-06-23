FCFS
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
