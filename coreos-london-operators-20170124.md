%title: CoreOS London January 2017 - Kubernetes Operators
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

\     :'#######::'########::'########:'########:::::'###::::'########::'#######::'########:::'######::
     '##.... ##: ##.... ##: ##.....:: ##.... ##:::'## ##:::... ##..::'##.... ##: ##.... ##:'##... ##:
      ##:::: ##: ##:::: ##: ##::::::: ##:::: ##::'##:. ##::::: ##:::: ##:::: ##: ##:::: ##: ##:::..::
      ##:::: ##: ########:: ######::: ########::'##:::. ##:::: ##:::: ##:::: ##: ########::. ######::
      ##:::: ##: ##.....::: ##...:::: ##.. ##::: #########:::: ##:::: ##:::: ##: ##.. ##::::..... ##:
      ##:::: ##: ##:::::::: ##::::::: ##::. ##:: ##.... ##:::: ##:::: ##:::: ##: ##::. ##::'##::: ##:
     . #######:: ##:::::::: ########: ##:::. ##: ##:::: ##:::: ##::::. #######:: ##:::. ##:. ######::
     :.......:::..:::::::::........::..:::::..::..:::::..:::::..::::::.......:::..:::::..:::......:::


-> # CoreOS London <-
-> ## January 24th 2017 <-

-> Luke Bond <-
-> @lukeb0nd <-

---

# WHO AM I?

- Developer turned DevOps engineer
- In recent years:
  - Lots of Node.js
  - Lots of Linux containers
  - Consulting, helping teams release more often with higher quality
  - Have closely following CoreOS's output, especially Etcd, rkt and Fleet
- Nowadays independant / freelance
- Currently working at the UK Home Office (Kubernetes, Docker, CoreOS, red tape)
- I enjoy demystifying and cutting through jargon to make things clearer
- I'm unnaturally excited about Operators

---

# WHO IS THIS TALK FOR?

- Those wondering what operators are
- Those who get the concept but unsure what building an operator entails
- Those interested in automation of operations on top of Kubernetes
- Those running stateful services in Kubernetes

- If you're already building operators then you may not learn much from this
  talk

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
  - They're like shepherds for applications that run on Kubernetes
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
- Explain the scenarios where those primitives aren't enough- that's where
  Operators come in
- Anatomy of an Operator
  - By dissecting CoreOS's Etcd Operator
  - ...and using CoreOS's "How can you create an operator?" guidelines
- Some code examples towards building a basic operator
- If there's time:
  - A future world of Operators
  - Example use-cases of Operators
