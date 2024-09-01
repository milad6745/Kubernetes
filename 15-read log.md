## read logs
kubectl logs -f  pod/nginx-deployment-576477d75f-4dj69 
kubectl logs   pod/nginx-deployment-576477d75f-4dj69 

```
   --all-containers=false:
        Get all containers' logs in the pod(s).

    --all-pods=false:
        Get logs from all pod(s). Sets prefix to true.

    -c, --container='':
        Print the logs of this container

    -f, --follow=false:
        Specify if the logs should be streamed.
```
