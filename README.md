1. Install Minikube: https://kubernetes.io/docs/tasks/tools/install-minikube/
2. Run it: `minikube start`
3. Open dashboard to monitor stuff: `minikube dashboard`
4. **ENSURE `kubectl` IS CONFIGURED TO OPERATE ON YOUR LOCAL MINIKUBE CLUSTER** (run `kubectl config current-context`)
5. Create services:
```
kubectl create -f Services/statsd.yml
kubectl create -f Services/graphite.yml
```
6. Create `statsd` deployment: `kubectl run statsd --image=hopsoft/graphite-statsd`
7. Create `logstash` deployment: `kubectl run logstash --image=jeiwan/logstashuhoh`
8. Create `bash-script` deployment: `kubectl run bash-script --image=jeiwan/statsduhoh`
9. Get the cluster IP: `minikube ip`
10. Visit `<minikube-ip>:31337/dashboard` and choose metrics `stats.bash.random.example`, `stats.logstash.random.example`. The metrics should show some random values.
11. Restart `statsd` deployment:
```
kubectl patch deployment statsd -p "{\"spec\":{\"template\":{\"metadata\":{\"labels\":{\"date\":\"`date +'%s'`\"}}}}}"`}}}}}"`
```
12. Check the metrics and see that `stats.logstash.random.example` stopped coming.
13. Get the Logstash pod name, log into it, and send a metric manually:
```
kubectl get pods | grep logstash
kubectl exec -it logstash-1685058003-rzdq6 bash
echo "logstash.random.example:77|c" | nc -w 1 -u statsd 8125
```
14. Check `stats.logstash.random.example` metric on `<minikube-ip>:31337/dashboard`: that manually sent metric should come.
15. Restart `statsd` deployment:
```
kubectl patch deployment logstash -p "{\"spec\":{\"template\":{\"metadata\":{\"labels\":{\"date\":\"`date +'%s'`\"}}}}}"`}}}}}"`
```
16. Metrics from Logstash should start coming again.
