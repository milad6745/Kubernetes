on worker node:
systemctl stop kubelete

![image](https://github.com/user-attachments/assets/fcdb2297-9f91-4876-b76a-cef721e9ab43)


## Service log


![image](https://github.com/user-attachments/assets/abb907ad-0169-497f-b3ed-c4b3e8129f6a)

kubectl logs pod <pod name>

## kubectl exec

  Get output from running the 'date' command from pod mypod, using the first container by default
  ```
  kubectl exec mypod -- date
  ```
   Get output from running the 'date' command in ruby-container from pod mypod
 ```
  kubectl exec mypod -c ruby-container -- date
  ```
   Switch to raw terminal mode; sends stdin to 'bash' in ruby-container from pod mypod
   and sends stdout/stderr from 'bash' back to the client
   ```
  kubectl exec mypod -c ruby-container -i -t -- bash -il
  ```

