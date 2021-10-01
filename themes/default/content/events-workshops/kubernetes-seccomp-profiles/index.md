---
# Name of the webinar.
title: "Kubernetes Seccomp Profiles: A Practical Guide"
meta_desc: "In this session we will explore the state of seccomp in Kubernetes and a couple of tools designed to make this more approachable."

# A featured webinar will display first in the list.
featured: false

# If the video is pre-recorded or live.
pre_recorded: true

# If the video is part of the PulumiTV series. Setting this value to true will list the video in the "PulumiTV" section.
pulumi_tv: false

# The preview image will be shown on the list page.
preview_image: ""

# Webinars with unlisted as true will not be shown on the webinar list
unlisted: false

# Gated webinars will have a registration form and the user will need
# to fill out the form before viewing.
gated: false

# The layout of the landing page.
type: webinars

# External webinars will link to an external page instead of a webinar
# landing/registration page. If the webinar is external you will need
# set the 'block_external_search_index' flag to true so Google does not index
# the webinar page created.
external: false
block_external_search_index: false

# The url slug for the webinar landing page. If this is an external
# webinar, use the external URL as the value here.
url_slug: "kubernetes-seccomp-profiles"

# The content of the hero section.
hero:
    # The title text in the hero. This also serves as the pages H1.
    title: "Kubernetes Seccomp Profiles: A Practical Guide"
    # The image the appears on the right hand side of the hero.
    image: "/icons/containers.svg"

# Content for the left hand side section of the page.
main:
    # Webinar title.
    title: "Kubernetes Seccomp Profiles: A Practical Guide"
    # URL for embedding a URL for ungated webinars.
    youtube_url: "https://www.youtube.com/embed/in_P293aXV0"
    # Sortable date. The datetime Hugo will use to sort the webinars in date order.
    sortable_date: 2020-10-08T10:00:00-07:00
    # Duration of the webinar.
    duration: "35 minutes"
    # Datetime of the webinar.
    datetime: ""
    # Description of the webinar.
    description: |
        Have you wondered what a seccomp security profile is, and how it relates to Linux Capabilities?

        Folks often dismiss seccomp profiles and Capabilities as a way of hardening applications as it is too difficult to determine what syscalls are in use by a given application.

        In this session we will explore the state of seccomp in Kubernetes and a couple of tools designed to make this more approachable.

    # The webinar presenters
    presenters:
        - name: Duffie Cooley
          role: Cloud Native Architect

# This section contains the transcript for a video. It is optional.
transcript: |
    Good afternoon, and welcome to the Cloud Engineering Summit My name is Duffie Cooley, and I'm here to talk to you today about seccomp security profiles I've been working in this space for about 20 years across a variety of different companies from networking to virtualization and most recently I've been working on kubernetes since about 2016. So for the last four years. As of this recording, I am unemployed. and looking and exploring what the next thing is going to be for me. You can find me most everywhere @mauilion, and recently I've been spending a lot of time on tgik.io and thepodlets.io both are great websites for keeping track of what's happening inside the cloud native space and if that's something that's interesting to you, I highly recommend you check them out.

    Let's get started with what is a container? I'm sure you've heard lots and lots of descriptions or definitions of what a container is. This is my personal favorite way of understanding what a container is. At the end of the day, a container is just a process running on a server somewhere. And the way that we can understand the mapping of that process, or the way that we can understand the way that process is isolated from other processes running on that same kernel is through the use of Linux primitives called namespaces. But understand, first of all, that that process is running just like any other process on the server. So there's nothing different about this process other than the mapping of namespaces that the process is constrained to.

    This is a group of namespaces that are associated with this particular process ID 2-4-5-2-7-5-4. Right, this process could be an NGINX running. It could be a NGINX container or it could be an SSH process from somebody logging into the server It can be any number of these things. And, this this mapping of primitives is going to be available against any process in the kernel, whether they are shared processes associated with the underlying node or whether they are containerized processes because you're running docker on that node. Now, as we look at this, one of the things I want to make clear is that if I were to like compare this output with the output of another running container, I would see that the mapping, right these the actual values associated with the CGROUP namespace, the IPC namespace, the MNT namespace and the NET namespace would disagree.

    They would be different values. And that's actually how we handle that isolation. We're basically tightly coupling that process with its own view of the network through the network namespace. And that's why when you SSH into a system and you type IP-add or use the all these interfaces, but when you exec into a container you only see one and it's the same node. We're using these primitives to isolate the view that that process has of the resources that are available to it, right? We can modify the view of the file system. We can modify the view of the inter-process communication system, right? the P-I-D namespace, inside of a Container P-I-D One is the very first process that runs. Outside of the container P-I-D One is probably sys init.

    The User namespace and U-T-S, there's lots of these primitives that are here to like help us isolate these processes from one another as they all share the same Linux kernel. The next thing I want to talk to you about is capabilities and capabilities is kind of a pretty interesting, not super new capability or new primitive that is available within the Linux kernel that allows us to be a little bit more granular. Certainly not a as granular, but a little bit more granular in the permissions that we can associate with a process. Now, let's break this down a little bit. So starting with kernel 2.2 the Linux kernel divides the privileges traditionally associated with super user into distinct units known as capabilities.

    So it's like, if you think about capabilities they have the ability to grant a chunk of permission to a user or to a process right? Before that, we had basically kind of a binary system when you were either going to be root and you had access to all the things or you were not root and you would only have access to those things that were kind of a traditional permission check where you, like, if you were associated with a group and that group had write access to a given file and you would have the ability to write that, write to that file.

    Same thing with read and also to modify or delete right? But what's different about this is now we can be even more specific, like maybe what we want to do is grant the ability to manipulate network interfaces to a process, but give them the ability to modify anything in the file system, right? And so we could give them a capability that would allow them to modify anything in the file system, but not let them access admin. So, you know, it's big chunks of permissions. Now, this is already a thing that exists within the within kubernetes and this is an example of how you might configure that inside of kubernetes.

    If this is something that you're interested in you can definitely go to docs.k8s.io an take a look at the security context topic and you'll find a way to actually explore and configure this stuff. In this example, we're actually granting net admin and sys time to this particular process and so the container that we've identified: gcr.io/google-samples/node-hello:1.0 has the ability to administrate the network, so they could do things like inside of the container dump IP tables or turn off of a network interface and those sorts of things. So how does all of this work? Capabilities can grant access to stuff, right? They have the ability to grant a very large chunk of permissions to a given process or to a particular user. Seccomp files are significantly more granular.

    They have the ability to filter or block access to given syscalls and they also have the ability to list, or to add or deny those permissions. And, the other thing I want to call out here is that seccomp is incredibly powerful, but it should be used in an allow and deny list model, otherwise, you will miss stuff. An example of this is that currently as of the 5.8 kernel, there are 345 syscalls that can be specifically allowed or denied by a seccomp profile in the x86_64 architecture.

    And this isn't like all of the things this is just the one that's for x86_64 and that number is up from I think it was like 250 in like a 4.x kernel right? So we're always adding more syscalls or we're actually further defining more syscalls and so you have to be really careful about the way that you define a seccomp profile and that you don't, it's not just a deny list because if you're denying only those things that you want to restrict, then as we add more syscalls, by default, they would be granted, and so we have to be allow and deny, we have to do both. If you want to see all of the syscalls that are available in the system, there's a great resource here and I'll show that to you in a little while.

    The next thing I'm going to do is I'm going to describe a tool that I'll be leveraging today to understand a little bit about the way that a given process is configured. And this was written by Jessie Frazelle, who's done a ton of work. Definitely one of my heroes in the space. She's done a lot of work on increasing the security of processes and docker containers and all that stuff. Here is an output of what kind of a bog stock configured docker configuration would work with right? And so, if we were just to do docker-run of a process and we take a look at the output of AMI-contained we’ll be able to derive a couple of different things.

    We’ll be able to see what capabilities have been granted and we'll also be able to see the syscalls that have been blocked. So by default, inside of, um, inside of a docker when you configure it or when you just you know, install it and turn it on. When you do a docker-run of a process, by default you get all of these system calls blocked. And a lot of these make a ton of sense. I mean if you, if we read through them. So for example, like there's one in here that is turning swap-on or swap-off, right? This actually gives us the ability to manipulate the swap file system as presented to the kernel, right? Which is probably more permission than we want to grant to a containerized process. There's other permissions like the ability to manipulate N-F-S, or the ability to init or delete or create a module.

    There's other permissions like pivot root, the ability to basically to root into a different file system if you can find access to it, and we do a lot of other filtering there as well, right? Like filtering the PROC tree, filtering the sys-FS tree. Lots of other stuff is actually filtered by docker by default. Inside of kubernetes, we have sort of a different output, and this is sort of an interesting thing, right? So, this is what happens when you do docker-run. This is what happens when you do kubectl run, right? When you start up a container we can see that some of these things are the same.

    We can see that capabilities have been defined and that's capability set is consistent. The apparmor profile is set to unconfined, which means that there is no apparmor running and if we look at seccomp profile, it says that it's disabled. Now, there are some blocks syscalls that are just inherent in the way that the container runtime inside of kubernetes operates. So, we still turn off things like swap-on, swap-off, okay, exec-load, some other kind of high-profile syscalls that could probably be more permissive. But at the same time we can see if there's a very large difference between these two values, right?

    So like for example, set systime is still available within the kubernetes container, but it's not set within, but it's not blocked, sorry. It's not blocked in the kubernetes containter, but it is blocked in the docker container. Why are these different? So folks like Jessie Frazelle and others did a bunch of research into a reasonable default seccomp profile and you can see the work, the result of that work, at docs.docker.com/engine/security/seccomp and they go into exactly why they blocked what and how and why they consider those things to be risks. So a ton of great work there and docker still uses this by default, but kubernetes disables that default.

    Kubernetes does this mostly because of the implementation detail of multiple containers in a pod, right? When you think about docker when we do a docker-run we're going to get one container and that process is pretty well isolated within that container. But within kubernetes, we have the ability to create multiple containers within the same pod. And that means that there's been, we had to think about the way that all of that gets manipulated a little differently. So what do we do? Like what if I actually wanted to make use of that default, that default seccomp profile that docker presents to us, right? Like what if I actually wanted to make use of that and just inherently increase the security of my processes.

    Well, before 1.19 you could do that with these sets of annotations. So you can annotate a deployment or any of the other primitives within kubernetes that allow you to deploy pods with these sets of annotations. And this is an example of something that you can make use of today, right? So, annotations, seccomp.security.alpha.kubernetes.io and at the pod level, you can describe runtime/default and that will configure your underlying container runtime to leverage that default set of permissions. And you can also specify this at a container level by giving the container name instead of the word pod. After 1.19 though and 1.19 is out now. So, after 1.19, you'll have the ability to do this with a first-class thing, right? So you'll be able to actually define this within the spec, rather than having to define the set an annotation level, you can define this right there within the spec at the pod level.

    So within the pod level you can just describe security context and there's a bunch of other stuff in security context that you can use to also further secure your applications. You can set this account seccomp profile and the type to run time default and that will also basically follow that same racing configuration path. And you can do that same thing with containers, right? So spec containers security context, seccompp profile, type: RuntimeDefault. Let's go back again for just a moment to what a kubernetes pod can do when you just do a kubte/ctl run and we could take a look at the output here. And remember there's like 22 blocked syscalls, seccomp is disabled, capabilities are all kind of what we expected and when we turn on RuntimeDefault we get 68 blocked syscalls so it's a lot cleaner and it's a lot more in line with what we were expecting from docker when we just did docker-run. So this is a, you know, very clear output.

    Alright, this is what you can do. If you don't turn that on, this is what you can do if you do turn that on and that really just basically increases the security model for that given process. So why do this stuff at all? Like why is this even interesting? And first I'll say that they're like three like pretty significant types of attack against containers that are interesting in the space today, right? The first is supply chain attacks. And I think this is a pretty important one.

    Like where did the container image come from? Where do the dependencies within that container image come from? And is there some way for us to validate that that came from a trusted place? Another one is exploitable application bugs, right? So if you, if I ran NGINX here and I gave the ability to like modify the content of the file system leveraging NGINX then that would be kind of a bug, right? And the other one that's interesting is like did some kind soul leave bash behind, right? Did somebody who created the configuration leave me a bunch of libraries that I could use once I actually land inside of that container or exploit myself into a shell? Is there, are there tools that I can expose or make use of right there inside that container to further exploit the rest of the system? And the last is syscalls against the shared Linux kernel.

    Now as I pointed out before, containerization is really just process isolation and it really does a pretty decent job of isolating the process from other processes against the Linux kernel, but it doesn't necessarily isolate that process from the Linux kernel itself, right? These system calls that the process can make, those can be pretty permissive on a given process level. And what these tools allow us to do is limit that output, right? Give us the ability to limit or constrain those syscalls that a process can make, which is a good thing. So to be able to actually pull this off, we kind of need to know what syscalls are going to be used by a process, right? And so there are a couple different ways to do this.

    One of the ways that has been around for a while is to leverage s-trace. So for example, if I were going to use curl, and I wanted to understand what system calls curl minus sS google.com would make, this is one way that I can actually go ahead and go about that, right? So I can do s-trace and pull all the syscalls for a given process. And here are the individual syscalls that were, that were called for this given process. And from there I can actually manipulate, I can create a seccomp profile and we'll look at some examples of seccomp profiles here in just a minute in the demonstration. And then that can test to make sure that my process is able to run and handle that thing. But, there are other tools out there and there's a bunch of, there's a bunch of them.

    One of the big ones here that I want to mention is DockerSlim, which is an incredible tool for allowing you in your build process, right, in your continuous integration or in your build process, to evaluate a container, to understand what syscalls and generate an apparmor profile for you, generate a seccomp profile for you, do all of those things for you. Kind of, um, programmatically within that thing. They call it DockerSlim because the other thing that it’ll do, is it will evaluate the underlying file system for that given container image under test, right? So you do docker-run or if you do DockerSlim of your container, you run your tests your integration tests against it that activates all of the code and everything within that container process and then DockerSlim is able to take what it learned about that process and remove everything that is not necessary.

    So if I had, for example, started with ubuntu and I had a bash in there, and a bunch of other things in there that could actually be used by an attacker, if I ran DockerSlim against that image it would be able to pull all of the stuff that I didn't use as part of my testing out of that image and give me one flat, minified image that contains only those things necessary for my process to run or operate as it did under test. So, very cool stuff and definitely worth checking out and keeping your eye on. Now demo time. What I've got, is I've got a kubernetes cluster stood up, leveraging my kind environment and kind is kubernetes and docker it's a way of actually bringing up a multi-node cluster locally with my docker containers on my Linux system.

    I'm going to be leveraging a docker image called an inanimate/echo or it's called echo-server and it's put out by Mario Lauria, incredible dude, and it gives us the ability to kind of just basically echo some of the things that we know about within the container system. And we'll be exploring those things as well. The last thing I want to tell, point out is that I have a new operator called seccomp operator. It's not mine, but it's being worked on within the community and it's hosted at sigs.k8s.io, seccomp operator. This operator gives us the ability to persist those seccomp profiles that we create down to disk so that they can be leveraged when creating containers. This is actually one of the problems that we have when trying to make use of seccomp profiles within kubernetes. So let's get started here.

    So first, couple of things: so the syscall profiles that I was talking, or the syscalls I was talking about before, this is a list of all of the system calls that are available within the Linux system across all the different architectures, right? And so here's the x86 one and minus-one means that this syscall doesn't exist in x86, but it looks like it does exist in arm. And this is another one of those things that makes this whole thing rather complex, right? As there are syscalls that go by a name or maybe go by a different number, depending on the different architecture that you're operating. So if you're leveraging x86_64 this is the column for you, but if you're leveraging arm64, it's a very different column and they might map differently, right? Like, the accept syscall in x86_64 is 43 and the accept syscall in arm_64 it's 202.

    When you're generating that seccomp profile, it's pretty darn important that you understand what the hardware architecture you’re targeting is and that you, and that you evaluate that. So, like I said, there are a bunch, many, a great many of differences of syscalls that are available. Last thing I want to point out is that there's been quite a bit of work recently in like, you know, making syscalls first-class. Like I said before, that difference between 1.19 and 1.18. And part of that work has been to improve the documentation around it.

    So if you go to the tutorials clusters, restricted container syscalls with seccomp, you'll find a very good tutorial for describing exactly how to go about that and if that's something that you're interested in, or if you want to play with it, this is a great way to jump into it. And they're going to take you basically through what I am about to do in the demonstration. Alright. So let's do it. Get nodes. So we have a four-node cluster or running 1.19.1 kube/ctl. Get pods. Dash A. We see that we were running the seccomp operator already. All these things are present. Let's go into pods and I want to show the difference between a couple of different pods. So let's do diff 1.18/fine-pod.yaml and 1.19/fine-pod.yaml.

    So these are the differences that I was talking about before like in the 1.18 file in the 1.18 timeframe if you wanted to secure a configuration with a specific seccomp profile, you would have to, you would have to describe like where to find that seccomp profile and you would do that with an annotation for that particular pod, leveraging this particular annotation title and then pointing at, pointing out where it would be found. In this case when we say local host we're talking about a particular directory on the underlying node, and that that directory is var/lib/kubelet and then whatever you have on it back here or actually var/lib/kubelet/seccomp and then operator, right? So, let's go to just take a look at that real quick and make sure that stuff is present.

    Var/lib/kubelet/seccomp and here we can see there is a operator folder and then there's also some configuration here, right? Now, this one is not going to be inside of that operator folder. If I do l-s, we're definitely not going to see the fine grained json yet, and that's because I haven't created it yet, but we're going to do that in our demonstration. Let's exit this node again. So again that difference just to highlight it, right? Is that before in 1.18 we had to do it with an annotation in 1.19. We now have first-class support and we can define it this way instead, and you can see the way it's mapping, right? So in the old one, in the annotation we would say it's a local-host-type profile and then we would give a path to where it could be found.

    In the new one, we would just say type, local host, and then we could give a path to where it would be found. Alright, great. Let's move forward. So because this is a 1.19 cluster, I'm going to jump into my 1.19 to find pods. I'm going to go first, I'm going to go back. Jump into my profiles and take a look at all of these profiles. So I've got a couple of different profiles that I'm going to describe here. The first one is going to be this audit.json profile. This is a really interesting profile because what it does is it actually allows us to log the syscalls that are made by a process. It doesn't deny them. It just logs them.

    And this gives us a way of actually determining what the, what's syscalls a processor is making within that runtime. The other one we have is fine-grained.json. This is interesting, this is a kind of an example of a security comp profile that gives us the ability to understand like the configuration here. So we've got our architectures x86_64, 86 and 32. We have a list of seccomp. We have a list of syscalls by name. And then we have an SCMP action allow, right? Now, up here we have default action SCMP, ACT, ERRNO, and that means that our default action is a deny, okay? Anything not in the allow list will be denied.

    So if the process within my container tries to make a syscall that is not in this list, then that syscall will be denied by that seccomp profile. Let's take a look at violation .json. And again we can see in this case, we just put a blanket deny. Everything will be denied. Alright. We got our three different profiles here. Let's go ahead and deploy them. And the way that I'm doing that, let's take a look at the demo profiles here. As I've taken all three of those examples, I populated them into the seccomp operator namespace as a config map and I've given them the name demo profile and I've given them an annotation seccomp.security.kubernetes.io/profile: “true” and this means that this will allow for the seccomp operator to grab these configurations and populate them on disk. So if we go back into our docker exec again, var/lib/kubelet/seccomp-operator.

    There we can see the demo profile and then we can see our three profiles that we created, right? So what all the seccomp operators doing right now is it's taking that config map that I created pulling out the actual seccomp profiles that I've defined and populating them on disk. And that's an important step because if they're not on disk and I reference them within a container then the container will not start because there is no profile available on the underlying node. Now remember that that doesn't keep me from making use of runtime default.

    I can make use of that right away immediately without making any changes playing with this seccomp operator stuff, any that stuff, what this is allowing me to do is put a more fine-grained a more specific seccomp profile to work for a given process. So let's go back over to our pods here. We're going to look at the bash runtime pod. And in this configuration, right? I've set use runtime default. I'm actually going to go ahead and run this bash process. I'm going to get it go ahead and run this can AMI-contained pause process and let's go ahead and start this up. So let's do, kube/ctl apply. Dash F. Bash. Runtime.

    Just make sure this works, right? So kube/ctl. Get Pods. So it does work and if we do kube/ctl describe pod and we can see that the configuration up here at the top, it actually backward populated the annotation, but inside of the configuration of that pod, it's now running under that. Under that, in that state with the, with the configuration of those seccomp profiles happening. So if I jump in here kube/ctl. Exec. T-I. Bash. And I do AMI-contained. Then we can see those 65 block syscalls. We see seccomp is set to filtering and so we can see that this process totally worked. Which is great. And if I were to do kube/ctl. Run it. Bash. Image equals. That same image.

    Then we can see the difference, right? So with runtime default very much more secured without runtime default, not nearly as secured. Okay? Kube.ctl. Get pods. Actually. So we can see our two pods, one running bash, one running [unknown]. Alright, so, next thing we're going to do is we're going to go ahead and deploy a more specific example. So let's take a look at find pod. Now find pods going to make use of the Maui Lion Echo Server. Then we're going to turn on all of the the the fine-grained configuration and we're going to see if we can get this thing running the way that we had it before, right? So we do kube/ctl. Apply. Dash F. Kube/ctl . Logs. Find pod. We can see that it's not operating correctly. It's not kicking up. And that is because, oh apparently I have set the path to something incorrectly. So we can see that in this warning error.

    It's telling me that the configuration, the seccomp profile that I've described, seccomp operator demo profile. Fine-grained. Contains .json isn't where it was expected to be, so we can actually modify that. So. Yeah, see the path is actually seccomp operators, seccomp operator demo profile. And so, alright, so let's do kube/ctl. Delete. Dash F. Fine pod. Kube/ctl. Apply. Dash F. Fine pod. Kube/ctl. Find pods. Now it's working. So it was just a passing problem. But anyway, so that's all working. And that's actually the demonstration that I had for you. This gives you the ability to actually configure a seccomp profile and the ability to make sure that that's working.

    So it's great. So the tools, and like if that is how that works, let's do one more. Let's do one more example of the violation pod here. Kube/ctl. Apply. Dash F. Violation pod. Kube/ctl. Get pods. And we will see that this will fail and we’ll see that the output is different, right? It's not failing because the pod wasn't able to be created. It's failing because of an error. So, kube/ctl. Violation pod. No logs. Kube/ctl. Describe pod. Violation pod.

    And we're not seeing any output, but we know inherently at this point we know it's because all of the system calls have been blocked and it means that we are not able to actually start any process because any system call that would be made against that Linux kernel would just be denied, right?. And so, the debugging isn't super great yet, but it does tell us that it was terminated in error and uh, and we can understand that it's likely because of the configuration of that seccomp profile.

    So that's one of the challenges that we have within this particular model is that the debugging isn't super awesome, but it is what it is. Alright, let's go back to our slides and let me give a shoutout to the amazing community behind the kind project. The Magnificent. Mr. Daniel Mangum who actually has been doing a lot of work on spreading the word on this stuff and actually doing a lot of the work behind the scenes for the seccomp operator. The amazing Paulo Gomez has put up a bunch of different, incredible talks on seccomp profiles and all of those things.

    And Sascha Grunert also just putting in the work to actually getting all of these things to the place where there can be supported within kubernetes. Here's my reference and slide. I'll leave this up for just a moment so that you can see where it is. Again if you wanted to if you want to see this content, the slides that are available at tgik.io. Uh, seccomp and you can find it there.

    Alright, so everything is, all of my references are here. Make sure you take your screenshot now so that you can go check those things out. They're all incredible. And thank you so much for your time. Find me online @Mauilion and have a great, great rest of the event. Cheers!
aliases:
  - /resources/kubernetes-seccomp-profiles
---