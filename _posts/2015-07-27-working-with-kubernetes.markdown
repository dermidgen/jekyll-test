---
layout: post
title:  "Working with Kubernetes"
date:   2015-07-27 04:58:00
categories: containers kubernetes
author: Nick Caruso
---

# General Blog: Working with Kubernetes and Impact to Illumineto

Danny turned me onto a new technology, so I spent a significant part of the weekend trying to wrap my head around Google's Kubernetes, which in summary is "an open source system for managing containerized applications across multiple hosts". Containerizing your application(s) provides significant benefits including deployment simplicity, high availability, and better leveraging resource services, to name a few. For more information visit here.

The rest of this blog is primarily for me to summarize my own understanding, the business impact, and also share details with the team.

 1. When creating a cloud platform there seems to be three primary options: 1.) Hosted VMs. This has been around for 20+ years in some form, and I'd call this Infrastructure As A Service (IAAS). 20 years ago it was pretty basic with a VM (or a blade in a managed datacenter - so not really a VM yet), but with AWS, Google Compute, and others, the offering has come with more advanced features than just hosting a VM, including load balancers, shared storage, DB as a service, etc. But fundamentally, it's still the responsibility of Illumineto to manage the entire VM (i.e. operating system and all applications in that stack).
  * **Pros**: A lot of developer flexibility as they can install any application on virtually any OS. The VM can also easily be moved to another IAAS provider, or run on-premise.
  * **Cons**: A tremendous pain for server admins to manage all these VMs, and conflicts within a VM of various applications. You also don't maximize resources, meaning you may have 10 VMs, but under utilized as 1 VM may be for your web frontend, but only using 10% of resources. So it's basically a waste of cost.

 2. PAAS - Platform as a Service. This has gained a lot of maturity in the last few years, and takes a lot of the Cons out of IAAS. A developer just uploads code to a PAAS platform, and the platform runs it. A simple HTML webpage, uploaded to Google's PAAS (called Google App Engine), may end up running on 100 servers across the globe. As a developer you don't care, and Google takes care of everything else.
  * **Pros**: Extreme scalability, redundancy, simplicity. You can go from 1 user to 1M users overnight (or within 1 minute). It also removes server admins from the organization. No one manages the servers, dbs, patches, etc.
  * **Cons**: While PAAS does standardize on language offerings, like GAE supports Python, Java, Go, etc, the implementation is still flavored to the PAAS provider, creating a challenging "lock-in" to that platform. Perhaps the biggest CON is the lack of flexibility for development. A developer may wish to use MongoDB or CouchDB, but the PAAS selected does not support that. Or there's a tool/library not available via the PAAS. A classic example for illumineto may be the need to run a Machine Learning algorithm that's in native C, and not available through a PAAS.

 3. Containers. I'm calling this virtualizing your individual application stack. Google's implementation of running containers is called Kubernetes, which is a fancy implementation for managing containers to scale them, provide redundancy, etc.
  * **Pros**: Provides the flexibility of a hosted VM for developers, with some of the benefits of PAAS in that the server admins don't have to manage entire VM stacks. Also takes better advantage of maximize resource availability.
  * **Cons**: From my perspective it's a huge learning ramp, with a technology that still in it's infancy, although gaining significant traction. If deployed correctly can provide significant benefits, but the key is "deployed correctly".

*So what does this mean for illumineto?*

There's no one "correct" approach, and probably the right answer is a hybrid of all three. But based on my research this past week, I'd like to lean on focusing on using Containers.

I'm not a huge fan of going with hosted VMs because they're just a pain to manage. I don't want to see illumineto managing 1000+ VMs, and us requiring a small army of admins.

My previous preference was going with the PAAS route because you don't have to manage any servers, or even any application. You just write code. But as noted above, there are significant limitations in flexibility, and long term it may not be a good business decision to be held hostage by a PAAS provider, which may not be attractive to any Strategics.

The Containers approach is very intriguing. If we primarily go with this approach, here are the business benefits to illumineto (in no particular order):

 1. By not going with PAAS we are more attractive for Strategics

 2. By not going with PAAS we are not constrained to a PAAS provider

 3. By not going with PAAS we can in theory provide an on-premise solution to an enterprise. Probably stating in theory is too harsh, but it's not easy.

 4. By going with Containers we provide new developers to the team a lot of flexibility to use the tools they want to use.

 5. By going with Containers we reduce the need (but not eliminate, server admins)

 6. By going with Containers we exemplify innovation to the public (i.e. investors, future hires, Strategics, etc)

I'm a very interactive learner, and unfortunately the BEST way I really learn is by getting dirty and making a bunch of mistakes. So my hobby project for the weekend was to run a Minecraft server, compatible with IOS devices, on Containers in Google Container Engine Kubernetes. Did I mention I've never stood up a Minecraft server before, and I haven't been in a Linux environment since college (almost 20 years ago), and Kubernetes was brand new to me? The good news is by 11pm Sunday night all was working, so now my 9 and 11 year old daughters can play Minecraft with their friends in a secure, highly available, and scalable environment :-). Probably complete overkill, but a great learning experience for me
