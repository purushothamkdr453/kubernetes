k9s is an cli based dashboard tool.

Installing k9s:
--------------------------------------------

Download the k9s package from https://github.com/derailed/k9s/releases page according to your operating system.

For linux/ x86:

wget https://github.com/derailed/k9s/releases/download/v0.22.1/k9s_Linux_x86_64.tar.gz

tar -xzvf k9s_Linux_x86_64.tar.gz

mv ./k9s /usr/local/bin

chmod +x /usr/loca/bin/k9s

execute "k9s" on the terminal which opens cli dashboard, displays all the options like context, cluster, user, k8s version, k9s version.

By default it displays the objects in default namespace.

type "0" -> switch to all namespaces
type "1" -> switch to default namespace

? -> Give all the options
Escape -> switch to previous screen

selecting(highlighting) and then press "d" -> Describe command of that object

selecting(highlighting) and then press "l" -> shows the logs of that object

selecting(highlighting) and then press "s" -> opena shell session to pod or object. ctrl + d to exit shell session.

type :object -> to display only those object details for example :pods -> display only pods

Ctrl + k -> kill object
