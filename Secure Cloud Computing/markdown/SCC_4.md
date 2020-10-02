# SCC 4

## Cloud security from an end-user perspective

Cloud security started to become a hot topic from 2009 on. Why? The big cloud providers launched they services in 2006 (AWS), 2010 (Azure) and 2013 (Google Cloud). These are just the big ones: Jair, who has a couple of computers at home, can claim that he has his own cloud and everyone can access. 

Who is the responsible for security in the cloud? After some troubles right at the beginning, nowadays AWS (and other big players) adopted the **shared responsibility model**: the customer needs to take care of the **security <u>in</u> the cloud**, while the cloud provider ensure the **security of the cloud**. This means that AWS makes sure that the hardware and software running on their site is secure against physical attacks or other hackings, while they are not liable for a customer who loses an encryption key or leaks some of their data. Things start to get complicated when talking about more peculiar things, e.g. who is responsible for the operating system? Well, if the customer is using a VM is theirs, while is they load a file on a cloud storage it is the provider's. However, in the first case the provider can also provide support and guidance for example ensuring that only up to date software is being run.

To improve the distinction of the various cases we can introduce the difference between IaaS, PaaS and SaaS (check slide for schema). Some examples: Dropbox is SaaS (the server is running in the cloud, you now nothing), Microsoft Office Online is SaaS (like Dropbox), a VM is IaaS (you decide whatever you want to do at OS level). An example of PaaS can be Google Cloud Functions, while Docker and Kubernetes are kind of in between IaaS and PaaS (you can decide a specific OS, or you can just ask for an image and do something with it).

Focusing again on security, "thing" as a Service can be distinguished with a bit more difficulties, sometimes it really matters to do what users are really doing with resources and the security responsibility is in between the provider and customer. Referring to the nice iceberg slide, very often the security of the cloud (ensured by providers) is just the upper part and there is a lot below the sea level.

Academia can focus on both security of and in the cloud, but we will focus on the latter. Another example: who is responsible for **data security**? In a docker-like case, customers are fully responsible, while in Dropbox we can assume a good level of security against attackers. However, Dropbox can still see the customers' data in clear! The bottom line is: by looking at these various example, we can come to the conclusion that responsibility is usually shared among provider and customer, but from an end-user perspective it can make sense to add an additional layer of security, to protect themselves further.

Against who are they protecting themselves though? The old cyber kill chain is not accurate for the cloud. The **MITRE** framework identifies different tactics and techniques, leading to endless procedures and it is open to new contributions. There you can find also tools, detection procedures and so on. It is a nice starting point to understand risks for different scenarios. Also, we can use the unified kill chain to model attacks as just cyclic actions happening to complete the actual hack.

Another useful resource is the **CVE** database, which are basically related to software versions (e.g. vulnerabilities of older versions that should have been patched). The idea of a CVE is to highlight a way that an attacker could use to break into a system and rise awareness in the community.













