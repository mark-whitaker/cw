# Jobs

## Setup

* Create a directory called `jobs` within `cw_labs\kubernetes` directory and `cd` into it.

---

## Exercise - 1 Batch Job

1. Create a batch job from below yaml with both `parallelism and completions` set to 1. Name the yaml file `job.yaml` or something similar.

> Open a separate terminal or a pane and run `kubectl get pod -w` so you can observe the pods coming up when you create a job.

When copying the manifest yaml across, make sure the `indentation` is intact. Or else, you will see unexpected errors when doing the apply.

```yaml
apiVersion: batch/v1 # Notice the api here that is different from one for deployment.
kind: Job
metadata:
 name: sleepyjob
spec:
 parallelism: 1
 completions: 1
 template:
  spec:
   containers:
   - name: busybox
     image: busybox
     command: ["sleep"]
     args: ["10"] # sleep for 10 seconds and then exit
   restartPolicy: OnFailure
```

2. Try changing completions to `4` and try `applying` again. Observe the error stating that the field `completion` is immutable.

3. Now change the name of the job to `sleepyjob2` as well as change the numbers for `completion` and `parallelism`. See below

```yaml
apiVersion: batch/v1
kind: Job
metadata:
 name: sleepyjob2 # Name change
spec:
 parallelism: 2 # Increase parallel pods that will be launched at a time
 completions: 4 # Total number of completions
 template:
  spec:
   containers:
   - name: busybox
     image: busybox
     command: ["sleep"]
     args: ["10"]
   restartPolicy: OnFailure
```

After applying (kubectl apply -f job-filename.yaml), observe the `watcher` pane and see how the pods come up and run to completion. (Time column will give you an idea as well)

---

## Exercise - 2 Cron Jobs

1. Create CronJob called sleepycron that runs pod busy box with ```sleep 5``` command every minute

    ```bash    
        kubectl create cronjob sleepycron --image=busybox --schedule "*/1 * * * *" -- sleep 5        
    ```
    # or

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
 name: sleepycronjob
spec:
 schedule: "*/1 * * * *"
 jobTemplate:
  spec:
   parallelism: 1
   completions: 1
   template:
    spec:
     containers:
     - name: busybox
       image: busybox
       command: ["sleep"]
       args: ["5"]
     restartPolicy: OnFailure
```
 Do an apply using `kubectl apply -f manifest-filename.yaml`
 
2. List all the CronJob, Jobs and Pods

    ```bash
      kubectl get cj,job,pod       
    ```
    Observe that at every minute, job and pods will be created
---