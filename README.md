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


by doing a kubectl get pods.
That's going to list out the current status
of our running pod right here.

Now, on the right hand side,
you'll notice that I'm reflecting three separate nodes.
Remember, a node in the world of Kubernetes
is a computer or essentially a physical computer
or a virtual machine that is going to run
some number of objects that we create inside of our cluster.
You'll notice down here at the bottom center,
I have something labeled called the "master".
On the left hand side over here,
we've got a deployment file
that create a pod or a service.
And then finally, up here on the top left hand side,
I have the docker hub that lists a couple
of the different images that we have created
throughout this course.
So let's walk through a series of actions now.
We're gonna start off with our deployment file down here.
Now, we're going to imagine that rather than creating
a single instance of the multi-client image
or multi-client container
maybe we have a deployment file that instead
says that it wants to create four copies
using the multi-worker image.
So we're just gonna imagine, 
that we slightly change our deployment file.
We're then going to imagine that we take this file
and we feed it into the kubectl apply command.
Remember, that is the command that we use
to load up the  files
that we had previously put together,
the client pod and client node port.
So when we run this command right here,
the file is taken,
and it is passed off to something called the master.
Now, on the master,
there is a variety of different programs,
three or four in total that control
your entire Kubernetes cluster.
So there are four programs in total,
but you and I are just going to kind of imagine
for right now that there's just one program
called the Kube API Server.
Again, four programs.
We're just gonna kinda consolidate it all down to one.
The Kube API Server program is 100% responsible
for monitoring the current status of all the different node
inside of your cluster and making sure
that they are essentially doing the correct thing.
So we're going to imagine we take this deployment file
and pass it into the master.
Kube API Server is gonna see this new file,
it's gonna read the configuration file,
and it's going to interpret it in some fashion
The master then has a little kind of notepad of sorts
that records all of its responsibilities
or essentially, all the things that you and I
have told it to do in the form of these deployment files.
So when we feed in the to deployment file,
Kube API server is gonna look at that file
and it's gonna see, 
The developer wants us to run four copies of multi-worker.
And so Kube API Server,
it's going to update its little list
of responsibilities and say,
"Okay, I need to be running an image
called multi-worker.
I need to be running four copies of it.
And right now, I am running zero copies."
So Kube API Server is gonna update its little list
of responsibilities right here, and it's gonna say,
"Oh, boy, I need to be running four copies,
and I'm currently running zero."
Well, that's definitely bad news.
So what's gonna happen?
Kube API server is then going to reach out
to the three different nodes that are running,
and it's gonna say to each of these,
We need to be running four copies of multi-worker,
but right now, we are running zero.
So you three nodes, I want you to start up
some number of copies of multi-worker."
It's gonna say to the first node down here,
"I want you to start up two copies of multi-worker.
I want you to start up one copy,
and I want you to start up one copy as well."
Now, inside of each of these nodes,
there is a copy of docker running.
That's right, a copy of docker running
inside of each of these virtual machines.

![Image](https://lh3.googleusercontent.com/22g1eCM0C7YuyeJchl8IgRVVvP6hs87jU-kofF0zDP19NPFFdRrMzX4rD9MsUfq0_o5FLoqhjMH7_7h3uR_uE1GZbRXqkAiAQldXSZWq5pJgULredfERn0YGDut7oIlSpYX_53PUvTraB7NgSBbKV1yAzF8ETCdHN2h0cGCO66irFFjdpneBzQFweJKMG-g7ebu8aNDCMBj1s7JY-2_FAS9OlnfWGnKqKXgj7UsLT2vD-XnWX8Km3DWHxOpqW4jhAXAQ9GqS_ALz1mjR1XATmSHna6gOmMMYwNOTwS8XfO48-zNoPSmcSpulXe4bXgYgNbd21AJVWsDTccI5PF5f_q_UATwSdS2u9AD32hMFoYyn2B2PGo1W7DEenE_be60OZLrNgQF6jO-T6Lzf4qkAKvCL_1U9GFI0-TaTHRuWG6omXkEAEgRswc6jLPjhEbzAACmrPiFXYdeBiVL7QwJ24YcfsfWctWNku2UnyK_SBrsW7juqFfRefkRGQoPeA3hpwwQlc5Ohoa5emq4tjEFQ-Fj08aO8k_BLZ1AqiNtspTT5eh8vazclkCm-pq1sSyx6Ivw3VHW7-sZyeJFjS0ab9agP6T2SPoGCeqnq94nZEKKFzlK0y0aQ2C84-V5mhaM4pTV4bqECrpBWPLK2qvIAo1v6T5AsTk0pECFk4KTMlCMyPkkYjhYvf57qtP-00EctLDIQmS_W5IWT1BJq3kQ8A3v0aOLx3U7Vx_5mywppSoDIWOrNLEfh8zExnBFzpSyA7dgALBSPuJ-w5XHX0I8u6wmdqurgLc2G5RZlg97uuqhNyR2yYd2mYtZ7dal2n38qEuSN07buyIVFfMYVzyfr1b1NeIqbvz1usf21WUt8DnW7tvWMYg3NJId2V3diitEFJRj_yJZ9FVjKpZK7TW2i9StEvp78CguwOJxkfRwycYJR=w1144-h668-no?authuser=0)

![Image](https://lh3.googleusercontent.com/sDSuOSwms7wTrHKW5DAcHvsoxqNaeP0XgOzaUlZZkkosB-VrDbH5ln5msEBwWgYAYECpFDYBQFGsKLn55XAZ-OtwlJmrZ4mMXDV9JFbMnXzMTIwYbPJgjab6_00jfkF0xTkhGvDMEvHeAzyNVBR79AR6cMyt-CNU8onMrHrYLd1Y0IyhZoHXFf9iD2OsCiyFrcTY4eO2U5SygV0UGh91MH0zcm6WBCz8qtTg6kbN7S8C5YGh2IhvKeb7n2k_1zIUkNFvQ6-G5l0MlXNHYiVuPUFaLM0A13HwPKbp9f0Sdpjykf5XGvgI4cWMKaeF7nqorIouVt3nKaYfwQwXWGI0i8T4Pb2JxcyPBv2aev9Nw-cH2NAIMxSHGxIEZtezSPY4U0BwSPY-sq0WA_tQxEX50ogpoB5jJRKGanKgxJf1YCiV0b7uJo2DwGFbGZqX6JbVuuIhizBYfFJPJ3a-HDgVQk2XDeLFRcsXYK7Irs6wYgcvlYB6DVKsEgzWFBhoMVjv6v4FUWrvZG4oWKoWvK67zdecRdFiPRQ5GTPY1oT7DGhNEmgfRkh73iz5ssccZIRm2UknkWxwx8Yj5pYSunayb1FxGA-dL4WVDiDjD419FoiOuqdXt9bHvxIEtl-WggdyozWzbxn75b5BkIo6chIVFKgiAqv8hp3YRigtdYgpoo5p8lWuAhzaASUJcakOo5s9reKLosGJCeTgeQLThZePRmxMlLC1g2hgST9Gkt2ykc8ugQE5t6PEuTmRjF9ppKEeDo5Nxv93Y6HgKecsjg4jx9CeRiTMoIIssSccUlwAMjhdGI_qFLUWie-SHNxcN5Rw7voLg0VBMY3s_0FuCPaxaKPFmuUvS-YN-i18eW3p-2VPgvZASy3G30fidBevplo-N1Z1PQYvgiekb2TIsE73rJuqDilBxKzBohRAC7RhpVe3=w1294-h365-no?authuser=0)


![Image](https://lh3.googleusercontent.com/TuYbOqvlg3h-LMioMywNUlsnzrI6Q08Ob7fKIrw4JSRrrOMISx4QqPExWHZWcWyxRWhYEOyMi88aFUea8v_DOeVTO_ez9x6BiN4tmQ-ftOJGM6d7w5vcVptV17h4CwpTxgrGi4bmbUqtn9dP1i3wlyboyXgTh1SWuSDAEjfDASMISz677r280I1GR8sM9jNPtFEZeBxCICbqllcWydhL5m1nhwK0l8A_3PGhLdJhyrxZfOzmEyiwEYAISqIgtukmO7zzvg8V5AUjrpeJQeVTh0g8GjxjDAaq7IkVtfiY1iD3jXDfjeCzJwlfbsVrlN6AHUkdUPwmzd-1qTaUFJxvmOSIPrniwXOqYrYp2Z3VGN1LD8HwvT8f_HSzoCqEU3LCav7pkoe4cPPGqD8zO0Aspe4ZLZ7ze5aTmoW0fPKcugNiK3zKNmh7Tz6N9gvjoLvoXFauEiwfNKqdBq6Y3xWHr_SqOPiYrD17_d3IvaY0PKUlJ_2SZoeT1fP3EgISvPc2T14nQqrYEmR1707F5wTJgYKTy968koSVIN--GDRTyV0oFcYrIUa0qZzPsU2cvg9md7OQuPfi7gOMUVWQPfInYaea9QkklSo4ivbdwYW-6dF1x_phMUaNvstCiQ0_rBByBXY5r26Z7EbazieY_v088wMjfc4zkXFapuYb3gUYWNi-eqW_ltNq8vY2Uvbv_ZOsA5q2F1kxkRPAC3QvnXpPVK0MpscmxoOVm0UjjLWf7_2xu0lnjsN295IwAtB4ZQ1Q0NC52sZvAX05WCJ5gQJVztbEdxuKPkeHhzGM-2yx2Bf1ckQTcZJPFzhbt0nVIF10vi_VIaYEUnFiu7T1wz1dV2f6syjcKjucoEHd_iPXvHgtX6TzRG1sDSb7SKQvb-xN2re3JA1d6VIGhqlVGr_2tvgH1QcQgvoSh46YfTZut04y=w1294-h568-no?authuser=0)

![Image](https://lh3.googleusercontent.com/aTDrFV8pqcAgxE1P1kkD9wwmYFl0GIb5aDsU9xA8y_oUNwukrz4mdFiQBGmWvaH8bHTVJumXLFh-8bBDBEqUHXDTvFMe1e4S5sU4VwFsxSgZtxvauoHf6dWZ3TlSIaublBLBxvJr7Vajius0gc6e9qnJbzdDO6unzqs7cjFFvrjpNAT5umcXk4csf8mZhEQHWZYSHOuCGr8H1-kKC5huQJUB6DCX44QgnGuSD0AhatkSGA-XJRaHQzfrXOUesyxmqp300NfmFD_giKPXB7Awr0HylaxSJej4KvwhiNVThnSwjcicw2Y4YF7PWckFlFdrhs82VWhP1XUSaTeLh1zGa_9d6r_wmm20U-BX2s5uReow97sWdGfJykz8hrWkfP6ORhPbWdyD-cBpozCaNtD-Bmuim5DIWcnPSdkk7876Dlnoh7tsim7QDhuTNRN7K7K-4fcCG6QpPVy6Ov3p4U1dAqUk-l9N6VBzxrrqth7si-MyGGjqnUXqrL25Mt9lKGyscHUyGgWAJh7ri08LKQk0E0jBCn3M2oX4pUt7yNGnrPBhUR6jqK1r35MvIXpeFf07qFR20QyYpp6SXUiU8-tLZmXy2Z5UuHHNFEmkbjzAIQHRZ6FLIJtkF2Yrx1ljT2HQUgnwRhQiv8DdME9XUkb5iq3nwqpe0zsIKbheIj6p-aAqzRW90o9BB3Wwreae0rxMoAreeT3Kj3Vk6WO2ZnDagcJ0S7TRClP_ZS8iIKbs1oR1MLxdhBclkrw2Vebh7jdX9Nc6uxCiTQHpUm5mHFZCEwo-f2lahknxN9t2Dhxqdy42MU2uYcqBnsscvqU0T0SKu7jd6cdd0sN__emTl_FZ2DnFFlyAo9P7WM3dKSUq0KYAqxdaK0N2Y1zVIdT83W4YeSO2ml-ZH2uvNWTTCVJWulblUUKrpMBKc0tqcIkFomk1=w1294-h557-no?authuser=0)

![Image](https://lh3.googleusercontent.com/vM_YV9NjTlLFY0HDTODjTAJqz1Kup2QZlgRrfXYeq9_YoSslQMzwSEE7pON885ubTDDUkO6uQE34MnXsq7Sy9ds69aL4hulfGFhv8j9ewhzyOiG4qv4CCpovTP8uV4ExCVbsgbs46crK_sOHLax3V4lUQvuwJK65hTl8NBzl4v7BezmnWvQ14uggblwZ5jofMDg1g-qbkljjtuFGe56yUJ6NvZqMA9CLE8CUBT05noizvIFfyicDzLB5UhNAuKkG4ttLqCeV42tXGtSGZrq7bGIpqV7AmozwWvhQD7FoJrn22kvw0Mvk7mUcctOGaFgfgjIxNKmskyOz9FZGC9mNzscjCptqQp-vUUo0xjkP_9j_A2hxrtYrmzLrmPAOrP_X5_g0vS3BRv1wGqcv0ndYy_0lRLpcrD8iacFTgUxLk4QCqm4sl3e63N-ybHsvLoPtdFHjY835zv_iBsjmFiOAo5jOYeHduTigMWGyQGJhLZt6-f1k4jRS1LvBzGtLFyBi4DkBBjRUicZ9i1gDiMJ_b4WZI8RkPeoq6Hf8EGlC4ZEt2QZNw08KqH-Ecdfh3L6Wt9JylIgxJvjobnI8NRIbbiVg-AcuM_CKKl7dRY2ly4HRAnFTjLbAiuLaTxaILN3mZ8hTOwU8hE85limnoWSluKCd_PuOl0CzP7OzLV9PRunQMBk1Qw5jl-2DA9rknlRXMbe2HuYu3rIlx776PI21glTBKWGaGS6iy7tAwbtESQZs366ILIt86oR43RZESGLjHij3E0GV2gU3H5SyowBQpLrXTcsiEX2c-vLP8lm2XdSsUOxOfaIDci_PkAObCUOqkcd3-P9oubBXfHihuVEGhzXI33c4Oi1kuvmvu4xV8FcaG_C8UJDlSjG407hp6tNErg4vbWLlZS-K0OEiDAHeZtlRzpB1rGzxWZ1EWoeG1MZp=w1294-h429-no?authuser=0)
