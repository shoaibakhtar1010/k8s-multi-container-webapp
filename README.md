# Kubernetes multi-container application

![Image](https://lh3.googleusercontent.com/tqDMnFsPdAJU5f4aizKmnZ8TGhq3CRbcxYGiAf3muOf4n8vt5CE_44z_b0rvYJKa4oAdfoJz3CpajaaBSs82ZOP99f1O604PXZ6yCW-CUqaEnz7RaQhjJMLni6qEu0QXD5PKsJb4eMo8cgQWr8t4mjeIM17BzK4tJOF407X6XiBL9Ps0MTRvy2EWZtdWHl64hjhB-BX_Xj7y4OHv3lXr_GvscujmQUBrCpYY5WohqKhDb8I4PxyPSRXklSA6IMW2xq2B5hUHyUMAZ4ITu3PK5sbloUXXbGSARvcEi0jDNPBZoL2SIiZdQNflcffb8q1h7liHUi-_f2jb-Srqx44MgwMzQVgi9zxXgPbtquRi6x-NefC_XI9KF_y69sORG03qv2L5e6j314H7FrwKVRzw3MgpkT4Hhuns_tlf203RWxz7AJMC53XBfYOxf6_DTkEZ99HGYGs--6ioWRywIoLu8P2zqnWsLU0a2jGVhla18OY2QAAThswhb0oooRpcJziDE_gJeBIMaTPVa7EAWBdEeD7uEUKihLrIGMjB5Y-80zhFIx-IBXKkJeM07WUZg2KmQevzKK9sYVLV7mnIwiHp9URypdaEVxwyKTiMrh8KRZ-Qc8wVqEhE498rMrQ1Tt_QIK4WGQy6xZoVOsxI-mxOrk6PfHVFesx99i9NI3QMr1q7MSneLWL5id7hQbQtJXnFWYzVPEgp1l8Z-3PGrLxpEWXZ7JlzJPb--Eu-NMYr_QMAHZkQReNWsxRgbhVJySPUrwCmgIaYh9kHxVYyjgi0oYyVtNFKwuJZDdkgY4yciJjcxSuxt7qsrptqhhjQufqbf8nQeB6ugdlZb_3M2ep45I94mKWnGYrNIHjsEEVxly49izCFdi5ll_UEQijwTOgw2MtiAxKEikxgE-eOY5G5vW_qVCupG0oM0zPzcnWnB8KY=w428-h256-no?authuser=0)

In the Elastic Beanstalk application
that we just put together.
Inside that application, we had four different containers
running at the same exact time.
We had Nginx, which was forwarding traffic to our server
in our client, and then behind the scenes we kind
of had that worker process running as well.
And of course, we had a copy of Redis and Postgres
running as well, but those weren't really containers
that were running with the rest of all of our other
containers as well.
They were outside services.

How would we scale up this application?

In other words, if we started getting a lot
of traffic and we started having a lot
of different users starting to enter
in different numbers to calculate the
Fibonacci sequence.
I would say
that it would probably be this worker container
that was doing the absolute brunt
of all the work inside of our application.
In other words, this worker process was using an algorithm
for calculating that Fibonacci values.


And so if we started having a lot of users making use
of our application, the most critical part to make sure
that this thing was working correctly
Now, ideally, what would've been really nice
if we had some way to easily spin up multiple processes
or multiple containers of that worker image.
And so if we had like three users come into our application
at the exact same time, it would've been great
if we had three different workers.
One of each could've been used to create
or to calculate a different Fibonacci value.
However, this would've been rather challenging to do
in the world of Elastic Beanstalk.
You see, in order to scale a application by default
with Elastic Beanstalk, here's what actually happens
behind the scenes.


So this is the typical scaling strategy
for Elastic Beanstalk.
Rather than creating more copies of a single container
Elastic Beanstalk looks at that docker-compose.yml file
and it spins up additional copies
of the entire set of containers.
In other words, we can kind of imagine that to scale up
our application, Elastic Beanstalk
would've created maybe two, three, four, five different
sets of all of our containers.
Now, unfortunately, we don't really have a lot
of control over what each
of these different groups of containers are doing.
In other words, on each
of these different groups of containers
we got an additional copy of nginx, an additional copy of the server and an additional copy of the client.
But at the end of the day, chances are
that the Nginx or the client or the server images
or the containers that we were running were not
going to be really the limiting factor of performance
inside of our application.
Like we just discussed, it was the worker container
that was the limiting factor.
And so it would've been way more ideal
to scale our application if we had the opportunity
to do something a little bit more like this.

Let me show you, it would've been just great
if we had the ability to spin
up these additional machines and just create a ton
of additional copies of that worker container on each one.
Chances are we didn't really need multiple copies
of Nginx or the server API or the client.
They were all relatively simple and for the most part
were just kind of serving up flat files
or responding to very simple API requests.



If we could have done something like this instead.

![Image](https://lh3.googleusercontent.com/SN4yGkUOXD9ohp3n-oRra7he76_O1Ai-qT5VSmdZ3nCDML_cELYLeNvfy3bmL_T3iuSJm5qQ7b2qEgJWtt01RX9DhOunf4TBw8tWMSoz65n67ObjfUh4X9_B47-2rYqHJJa-hA5YPmWQPdeN7juYFDY0D808ODi7Fn8goP6qjgDEGS3CpvhYyjDzk2e4S5_p4_vEqhUO0HF0ixek04pamwa44l_fPHIghHp8MFpUZoJYEQDbY3yowbX6PK4cQWTFn9Jg3EDJutXrPU_4RafphTEeBu4LXjQaSCPHDiqwypdYNnWxMIK1yjtzgwZv4WfLTei8pCsqy8HaWIs-jneOEBsAlDWhkG8PlU_Pm-t0VN-9K4RREfTT9FS67ZEwVJro5LyT7pS_Lf_6Twq1CUEOTLZmlE2yfSYV4E1bs2c1WlVaZVyCU6NWkmagb7fSMNW4JRZVVuBSYQh6_Pls4jsq2Bqg484jIZt5Z773ghH4dzWCklGCSh6MSqPdTmamog9e6KAQQt02tWiIY27YGojCEDHwJu3fcnwBI5K6EJHFuMeKZYSTAZ1Ezvyn-vukcchhqc4xu8ZAf6lFQgsWVhDwm3-hC-_ysLCMEhaPareaA3-XwEuZ39YL4S-yzON8rU9NgMPMajS4yHhGMnKq4peXjVsfcJfP5OE6AOJ7h72ioDGO0vycN0BC0yHBawp3Itlwx0MBFMJjqMcwk-gQs06yUIaeGIogPROniO7GPZdmcpu7R7EuwJoAeqHN9kPob-OmdrRjNboHYSufSZkXBL9dCFkxC9scFb-iWBzDAHU8V89hEs0awU3MVsCdmXJ637Ln0VPvjfbnmEB_17miFuedesjglbNSaLqI0i38MAgvDfOZVqm8vrl1M7UmuSNDuT6q1TGtZNjJzTzxvptXk8_om29JuKT77xHnJUdNUNZr5TdO=w1294-h607-no?authuser=0)

So as you might imagine, this is the type
of approach that Kubernetes would allow us to have.
If we were making use of Kubernetes,
we could have had additional machines running each
of our containers, and we could have had a lot
of control over what these additional machines were doing
or what containers they were running.
So let's take a look at a quick diagram
of how Kubernetes could have been used
to solve this entire scaling issue.


So what you see on the screen right here is a diagram
of a very simple Kubernetes cluster.
A cluster in the world of Kubernetes is the assembly
of something called a master, and one or more nodes.
A node, which is each of these blue boxes right here,
are a virtual machine or a physical computer
that is going to be used to run some number
of different containers.
So each of these different virtual machines
or physical computers that you see right here
can be used to run different sets of containers.
I wanna be really clear that each of them
can run different containers.
Like different images or even different numbers.
So for example, the node on the far right hand side
is running three separate containers,
and the one in the middle is running just one container.
These can be completely different containers.
They don't have to be identical.
And so in the world of Kubernetes, you can imagine
that we could have very easily gotten closer to a kind
of scaling flow like this right here.
Or maybe we had just one node or one virtual machine
that was running Nginx, our server in our client
and then maybe we had a bunch of additional nodes
or virtual machines that were running
copies of our worker image.
![Image](https://lh3.googleusercontent.com/w8b1V28yoZH89uFj4-BgBSlBJDM6PcTzVy_ld8TsgBTQC-BGe0mpPzVyOQyl7kTNJ9ZSg8jiqFzm7APxKHG3u_O5gcnIRw4_33zYtjGOMHidpuoYdPTXExL6a5DKvqy6Ukv_fKahiiKUGrUfYqHXsTq5kLIr1Oj28bKmiGBmyuZbPSa9I7YrF86BMG5oAEzOmNlEDtHYy3BRnjMFoNou1qN6GgtmclhXpdcGZm0qpkoaGxfvHkYbsIoR6LkCzxLteleP_J2KPg13GdMY3q4RAyeuhUK7ZFUaFwDgFKfStnGFsG_Ut4bmFVGx6uQfdhbpQQneDmLPn2b9uBK4qkfmZ8GI6YpqBIABqZGmeQLQ5PnPAiZda8Ma87yW8W-_lkLOFkmvanfNAOqyWYF4sUUNUuMfp__-k3dhBo3c0iJ1eLmoGfTMzUJyD0F_6xm-VGfpLoqu2S5_T7ZBfgR5zYEssDDEIHBKHXRcUfg-8hk4j6l3mMMRnxmacOb1o4IZ7TxM-pIk5TCGb0cHx5aLVYDl0om45eQ8SXMctAXcOb5Dk0c1YTT9v0eXdZRCKzi3KQVO9VBoM6cllUx4VjbkWU07LcDAsLJFWS9t_auQuKpHNtAA4hLptn0Yv6sFCxlUuS_4DYvuQS00QqZwiwXSsYlgyJ13o5TYaMjEf9wjJUrRRsPgkgUVf6gj2Nx5bY4fMMMHvz5IvoiuZWccm25VzEwXNtzR4hc-8IhbbhjJ7L9MQ6DhFU9Sia_SeZckn3NlzuI4Lz4RxGF5vFxWdheS1U1ovwZimLtcQzBspcyt_qn_HkSmbJxba_5pI0hRaGIX09MTYzDjtOKij5yR7ZRnyd1AY6j4YhUA0WPT5DM7dZuCuLDvsQSH65hjN8VLKQ8bVFhj7HRC9ID4NEExrczbFz-TdfGArbnavFfL3GLy3SuZZW-M=w766-h668-no?authuser=0)

Now, in the world of Kubernetes
all these different nodes that get created are managed
by something called a master.
This master down here has a set
of different programs running on it that control what each
of these different nodes is running at any given time.
You and I as developers interact with a Kubernetes cluster
by reaching out to this master.
You and I give some set of directions to the master.
The master receives the command
and then ultimately relays that command
to all of these different nodes.
Now, outside of our cluster, which is represented
by this gray block box right here, we have a load balancer
which will take some amount of outside traffic
in the form of network requests and relay all those requests
into each of our different nodes.
Now, one thing that's going to be a big point of discussion
for us is exactly how this node balancer works
on Kubernetes that we're going to go through as well
on Kubernetes and what it is and what it does for us.
So at this point, I think we've kind of established
that Kubernetes is a system
for running many different containers, so different types
of containers, different numbers of containers
over several different computers or virtual machines.
And we would choose to use Kubernetes
if we had the need to scale up our application
and run multiple different types
of containers in different quantities.


![Image](https://lh3.googleusercontent.com/cbyn_8F5wTu86rrunpktJmVLcg73XnlD7vsBLpTTfHP4zpdgWnPc7AK270AcdEUq3BapG6AnkalV_R2QO6FhNvZy0O7j7rHd649W9WnJvjqSI01fVQvGz5IA98eYvvI7IYVP4NbKHEMlOmSMYXLs92fSv24HdOtae_tOSKkpHJlPDaav9cUyR0tEHeYTjrTW_XueUDomcHV9AA68SXaF8oO0GM7DIhXkTwzq984k5vcj3C9OGEaMjfmssMRUcKNwcZShO1wssIGn47aXTW27vFQllOs1h_XtGO3XudfE6NMF11Hx6Nd05vKO2KetIDJjOdF8tmgRdlmZQbtRSBWiDkfE6EWiT0Pw8nGpnQJadCAXbnNw7qFm_dNBIglr-l28NcGC8IGoTP7EnArmreVRympCJn1IXXuwXy-B1zYxFhbOPdi_wXmTrTS6X3LQYvzrIKOPe00-5ImubXnoOOP4bfjsD8y7CRH7e5UVGFZ6z3NvACYnjQE2SCPU7yyHwzy9vERD9VPaTLha0phxBTaC9-x89M4T_hKHq__0RwVA3eK-t4ilPj4KOebNjuub504qfNzI7_1uf8e35lPeybF8xG3tQZOnyiY_9V9y12Kz68UqyyZ_2-lDuBJmcemwHJz5q_BnN2u37xEFt2ealNPQc_OEClTR0gd2SHmUeNqBm8_XursM6nmVNgzaq5B3pMCBKwOPzSS7mXd58AyoIROzyPfVa1aNbCSNfg7t_YpFB9GvudyjrBhmO_S6pozT9JYQ9fvuqM6LADF6-E7GilAENkZBr4FbX-YcqRQ02LYh1MQfjSnh43Q-26f8W2cBc5WHK7t3LmiZfPDOYNVj1N1EyIqx1BaA_CHITOxoSfPUf9X7aOMdYPuMkC-PqhK9uVRqbtwomoOGNUY8wMCRqCD_9Tc8g8QZEPpPEDguKxZ3nAgB=w560-h243-no?authuser=0)


Again, we want to use it anytime we expect to have
an application that consists
of many different types of containers,
running in different quantities
on multiple different computers.


