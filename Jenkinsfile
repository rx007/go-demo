node("cd") {
    def serviceName = "go-demo"
    checkout scm

    stage "Deploy"
    dockerFlow(serviceName, ["deploy", "proxy", "stop-old"])
    stash includes: 'consul_check.ctmpl', name: 'consul-check'
}
node("swarm-master") {
    stage "Health"
    unstash "consul-check"
    sh "sudo consul-template -consul localhost:8500 \
        -template 'consul_check.ctmpl:/data/consul/config/${serviceName}.json:killall -HUP consul' \
        -once"
}
