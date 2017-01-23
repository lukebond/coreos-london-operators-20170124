%title: CoreOS London January 2017 - Introducing Kubernetes Operators
%author: @lukeb0nd
%date: 2017-01-24

\           \_____ \__   \_ \_______  \______  \_____  \______  \_     \_ \_______ \_____ \__   \_  \______
             |   | \\  |    |    |\_____/ |     | |     \\ |     | |         |   | \\  | |  \____
           \__|\__ |  \\_|    |    |    \\\_ |\_____| |\_____/ |\_____| |\_____  \__|\__ |  \\\_| |\_____|
 
'##:::'##:'##::::'##:'########::'########:'########::'##::: ##:'########:'########:'########::'######::
 ##::'##:: ##:::: ##: ##.... ##: ##.....:: ##.... ##: ###:: ##: ##.....::... ##..:: ##.....::'##... ##:
 ##:'##::: ##:::: ##: ##:::: ##: ##::::::: ##:::: ##: ####: ##: ##:::::::::: ##:::: ##::::::: ##:::..::
 #####:::: ##:::: ##: ########:: ######::: ########:: ## ## ##: ######:::::: ##:::: ######:::. ######::
 ##. ##::: ##:::: ##: ##.... ##: ##...:::: ##.. ##::: ##. ####: ##...::::::: ##:::: ##...:::::..... ##:
 ##:. ##:: ##:::: ##: ##:::: ##: ##::::::: ##::. ##:: ##:. ###: ##:::::::::: ##:::: ##:::::::'##::: ##:
 ##::. ##:. #######:: ########:: ########: ##:::. ##: ##::. ##: ########:::: ##:::: ########:. ######::
..::::..:::.......:::........:::........::..:::::..::..::::..::........:::::..:::::........:::......:::

\   :'#######::'########::'########:'########:::::'###::::'########::'#######::'########:::'######::
   '##.... ##: ##.... ##: ##.....:: ##.... ##:::'## ##:::... ##..::'##.... ##: ##.... ##:'##... ##:
    ##:::: ##: ##:::: ##: ##::::::: ##:::: ##::'##:. ##::::: ##:::: ##:::: ##: ##:::: ##: ##:::..::
    ##:::: ##: ########:: ######::: ########::'##:::. ##:::: ##:::: ##:::: ##: ########::. ######::
    ##:::: ##: ##.....::: ##...:::: ##.. ##::: #########:::: ##:::: ##:::: ##: ##.. ##::::..... ##:
    ##:::: ##: ##:::::::: ##::::::: ##::. ##:: ##.... ##:::: ##:::: ##:::: ##: ##::. ##::'##::: ##:
   . #######:: ##:::::::: ########: ##:::. ##: ##:::: ##:::: ##::::. #######:: ##:::. ##:. ######::
   :.......:::..:::::::::........::..:::::..::..:::::..:::::..::::::.......:::..:::::..:::......:::

-> # CoreOS London, January 2017
-> ## Luke Bond

---

# WHO AM I?

- Developer turned DevOps engineer
- In recent years:
  - Mostly Node.js and Docker
  - Consulting, helping teams release more often with higher quality
  - Have closely followed CoreOS's output, especially Etcd, rkt and Fleet
- Nowadays independant / freelance
- Currently working at the UK Home Office (Kubernetes, Docker, CoreOS, red tape)
- I enjoy demystifying and cutting through jargon to make things clearer
- Hobbies include home-brewing and making headings with figlet
- I'm excited about Operators

---

# WHO IS THIS TALK FOR?

- Those wondering what operators are
- Those who get the concept but unsure what building an operator entails
- Those interested in automation of operations on top of Kubernetes
- Those running stateful services in Kubernetes

- This is an introductory talk

---

# WHAT ARE OPERATORS?

- Maybe you read the CoreOS Etcd Operator announcement blog post
- Maybe you watched some talks by Brandon Philips
- Maybe you listened to Brandon on the Changelog episode "Understanding
  Kubernetes Operators"

--> But maybe, like me, you were still left scratching your head a bit! <--

---

# WHAT ARE OPERATORS?

There are some obvious things:

- Operators encapsulate operational knowledge in code
  - The kind of stuff a sysadmin knows about a service, but automated
  - Maybe they're like shepherds for applications that run on Kubernetes?
- Operators leverage the Kubernetes API and primitives in order to do this

---

# WHAT ARE OPERATORS?

But I was left with a few questions:

- Doesn't Kubernetes already magically look after my services and will
  restart and migrate them as necessary?
- Doesn't Kubernetes already have primitives such as StatefulSets and
  ReplicaSets to help with this stuff?
- How are these things actually built?

If I was confused about these things then maybe you are too. Hope this helps!

---

# IN THIS TALK

- I aim to answer the questions of the previous slide
- I'll explain the relationship between Operators and Kubernetes primitives
  such as StatefulSets, ReplicaSets and Services
- I'll explain the scenarios where those primitives aren't enough- that's where
  Operators come in
- Anatomy of an Operator
  - By dissecting CoreOS's Etcd Operator
  - ...and using CoreOS's "How can you create an operator?" guidelines
- Some code examples towards building a basic operator
- If there's time:
  - A future world of Operators
  - Example use-cases of Operators

---

# THE NICHE FOR OPERATORS - WHAT KUBERNETES DOES AND DOESN'T DO FOR YOU

- Let's say you have a 12-factor web app. Kubernetes will:
  - Keep it running; surviving crashes and node failures (ReplicaSets, ReplicationControllers)
  - Scale it up and down when you want it to (ReplicaSets, ReplicationControllers)
  - Internally load balance traffic to instances (Services)
- Stateless apps can be destroyed, moved and upgraded easily anytime
  - Existing Kubernetes primitives are perfect for this

---

# THE NICHE FOR OPERATORS - WHAT KUBERNETES DOES AND DOESN'T DO FOR YOU

- Let's say you have a clustered database, however:
  - Can't be rescheduled on any host like stateless services
  - Instances need to stay with their data
  - Scaling may not be as simple as adding more nodes
  - Specialist knowledge is required to effectively manage and operate each
    databases

---

# THE NICHE FOR OPERATORS - WHAT KUBERNETES DOES AND DOESN'T DO FOR YOU

- This is where operators come in
- They fill the gap of the application-specific things that Kubernetes can't
  do for you
- They extend and leverage existing Kubernetes primitives and functionality

> An Operator represents human operational knowledge in software,
> to reliably manage an application

- Complex, manual operational tasks become a single command or line of config

---

# CREATING OPERATORS

CoreOS have published some guidelines on creating operators:

https://coreos.com/blog/introducing-operators.html#how-can-you-create-an-operator

Let's work through each item in their list, imagining that we're building a
Postgres Operator.

Code can be found here: https://gitlab.com/lukebond/postgres-operator.git

Mostly this is copy-pasta from the Etcd operator!

---

> # 1.
> Operators should install as a single deployment e.g.
> `kubectl create -f https://coreos.com/operators/etcd/latest/deployment.yaml`
> and take no additional action once installed.

    $ cat kube/deployment.yaml
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: postgres-operator
    spec:
      replicas: 1
      template:
        metadata:
          labels:
            name: postgres-operator
        spec:
          containers:
          . name: postgres-operator
            image: quay.io/lukebond/postgres-operator
            env:
            . name: MY_POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace

---

> # 2.
> Operators should create a new third party type when installed into
> Kubernetes. A user will create new application instances using this type.

        tpr := &extensions.ThirdPartyResource{
                ObjectMeta: k8sapi.ObjectMeta{
                        Name: "postgres-operator.lukeb0nd.com",
                },
                Versions: []extensions.APIVersion{
                        {Name: "v1"},
                },
                Description: "Semi-automatic PostgreSQL cluster",
        }
        _, err := config.KubeCli.ThirdPartyResources().Create(tpr)

Demo!

---

> # 3.
> Operators should leverage built-in Kubernetes primitives like Services and
> Replica Sets when possible to leverage well-tested and well-understood code.

- The Etcd Operator uses a ReplicaSet with `replicas=1` to keep the backup
  running
- But then reconciles cluster size itself (instead of using a ReplicaSet)
  - This is because cluster scaling tasks are specialised, not as simple as
    adding and removing pods

Let's look at some code!

---

> # 4.
> Operators should be backwards compatible and always understand previous
> versions of resources a user has created.

---

> # 5.
> Operators should be designed so application instances continue to run
> unaffected if the Operator is stopped or removed.

---

> # 6.
> Operators should give users the ability to declare a desired version and
> orchestrate application upgrades based on the desired version. Not upgrading
> software is a common source of operational bugs and security issues and
> Operators can help users more confidently address this burden.

- For upgrading the version of the software being operated (e.g. Etcd, Postgres,
  etc.)
- Perhaps this could be used for schema migrations as well as version upgrades?
- Declarative rather than imperative configuration is a solid part of Kubernetes

---

> # 7.
> Operators should be tested against a "Chaos Monkey" test suite that
> simulates potential failures of Pods, configuration, and networking.

The Etcd operator has such a chaos monkey type test approach:

- It's simple but effective (about 100 LOC)
- Will kill Etcd pods every X seconds with Y probability
- Just think about how much your distributed system would gain from this
  simple test addition!
  - Soon this will be the norm for distributed applications
- Easy to translate into other applications, e.g. Postgres, etc.

-> Let's look at a bit of the code

---

# A FUTURE WITH OPERATORS

- Operators are a step towards NoOps
- Self-operating and self-healing systems and infrastructure already exist
- IaaS, Docker and Kubernetes enable a revolution in this space
  - The building blocks are now in place to make this possible
- The emergence of the Operator pattern is an early attempt at a standard way
  to build self-managing systems on top of Kubernetes
- People are still keeping databases out of Kubernetes
  - I think we're running out of excuses to do this

---






-> # Thanks!!

-> ## Any questions?





-> Slides can be found here: [https://github.com/lukebond/coreos-london-operators-20170124](https://github.com/lukebond/coreos-london-operators-20170124)
