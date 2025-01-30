# Individual Assignment 2 - Configuring and Managing Your Firewall

> This assignment will NOT include detailed step-by-step instructions. You should take the time to explore the IPFire interface to discover how to implement the changes described in this assignment! Assuming you have completed Assignment 1, you already have the environment needed to complete this assignment.
>
> You can (and should) use the Internet as a resource if you are unsure of how to proceed with some part of the project. The [IPFire Documentation](https://www.ipfire.org/docs) is an excellent place to start! 
>
> If you are completely stuck and unable to progress at all, please reach out to your classmates or to me as a last resort.
>
> One useful hint is to make use of IPFire's configuration backup - the same thing you did to submit Assignment 1. You can restore this configuration at any time into IPFire if you have messed something up. 
>
> Another useful tip is that it is possible for you to take advantage of the *snapshot* feature of VMware. **Before** you begin working on this assignment, **power off** your IPFire VM and use VMware to take a **snapshot**. You can then always revert back to that snapshot any time if you find yourself in a situation where you're completely unable to even reach the web interface of the IPFire firewall.

In this exercise you will use your IPFire firewall setup to explore and experiment with various configurations that can effect different behavior on your network.

You will accomplish the following tasks in this assignment:

* Assigning a static IP address in the firewall to your client VM.
* Blocking network traffic from within the private network (i.e. your client VM) to a specific site on the Internet
* Using port forwarding to enable access to a service running on your client VM.

## Phase 1: Static IP address

DHCP (Dynamic Host Configuration Protocol) is the protocol used to assign dynamic IP addresses to your computers.

In assignment 1, you reserved a few IP addresses on your network and did not include them in the DHCP address pool. You'll now assign one of those addresses to your client virtual machine as a *static IP address* within the network.

* Discover the MAC address of your Ubuntu client VM. You can do this from VMware's configuration screens, just as you did in Assignment 1 to discover the MAC address of the Red and Green interfaces.
* Use IPFire to assign one of the static IPs that are within your subnet but that you did *not* included in your DHCP pool to the VM. 
* Reboot your client VM. It should then have its new static IP address!
  * Verify this by opening a terminal in Ubuntu and typing `ip addr`. You should see the IP address of the interface in the output - it should match the IP you just assigned. If not, check the MAC addresses and try again!

## Phase 2: Traffic Blocking

> My my, your roommate has sure been an unproductive student today! Playing games, watching TikTok videos, and chatting the day away on social media instead of working on your projects! 
>
> It's time to help your roommate with their productivity by simply blocking access to one of the sites they're wasting all their time on!

* First, you need to discover the IP address of a site you want to block. You can discover the IP address by making a DNS request using the `nslookup` tool on your *host* system. (The Ubuntu VM may not contain the necessary command. While we could install it, that's outside the scope of this project.)
  * Think of a site you want to block. While we're making a joke about social media blocking, it's actually a bit more work to fully block many large scale sites due to the fact that they use *multiple, rotating IP addresses* for high availability. 
  * On Windows, open a terminal by looking for `cmd` in your Start menu; on Mac, open a terminal.
  * Run `nslookup yoursite.com` - obviously, replace `yoursite.com` with the domain of a site.
  * If you see *one* or *two* IPv4 addresses, this site may be a good candidate for blocking. If you however see *many* IP addresses, think of and try another site.
  * Example:

        PS C:\Users\fmillion> nslookup tiktok.com
        Address:  10.0.2.3

        Non-authoritative answer:
        Name:    tiktok.com
        Addresses:  23.56.99.49
                23.56.99.17
                23.56.99.64
                23.56.99.105
                23.56.99.51
                23.56.99.19
        
    TikTok would be a poor choice because it has many IP addresses. However:

        PS C:\Users\fmillion> nslookup minecraft.net
        Server:  UnKnown
        Address:  10.0.2.3

        Non-authoritative answer:
        Name:    minecraft.net
        Address:  13.107.246.38

    Here we see that Minecraft.net only has one IP, so it'd be a good target for our block.
* Before you create a firewall rule, *make sure you can access the site* ahead of time. This way we'll know for sure that your rule actually took effect, and it isn't just a connectivity issue or another bug!
* Create a **firewall rule** in IPFire:
  * The *source* should be the Green network - your internal LAN.
  * The *destination* should be the IP address you discovered.
  * The action should be `DROP`.
* The rule should take effect immediately. You should be able to go to your Ubuntu client and try to access the website of the service you just blocked.
  * If it fails - great! It worked!
  * If you're still able to get through, verify that you did everything correctly, and if it still isn't working, try another site.

If you are so inclined, you could also make multiple firewall rules to try to block all of the IPs of a service with multiple addresses. However, this may not work reliably as DNS servers often select *random* or *rotating* addresses from a large pool each time someone does a lookup of the domain name. 

You don't need to focus on a "non-productive" site - you could simply try blocking the MNSU website. However, please try to think creative and try something else!

> **It goes without saying that you should NOT do this on your actual router to your roommate, friend, partner, etc. simply because you feel they're being unproductive! The scenario presented above is for humor purposes only and is not to be taken seriously. Remember to always act ethically and responsibly.**

## Phase 3: Port Forwarding

In this section you'll start a service on your Ubuntu virtual machine and then try to access it from *outside* of the private network - i.e. "from the Internet".

* You've assigned your Ubuntu VM a static IP address. Make sure you know what that IP is.
* On your Ubuntu VM, you can start up a very simple web server:
  * Run `python3 -m http.server 8080`
  * If Python isn't installed, try running `sudo apt -y install python`.
  * Ensure the site is working by opening `http://localhost:8080` in the browser.
* Now, on your firewall's interface, add a new rule as follows:
  * The source network should be the Red network.
  * Select "Destination NAT" for the NAT type.
  * External Port should be the port your firewall will listen on. Set it to a port **other than 8080** - for example, 8008 or 8888. Don't use a port below 1024 or one above 65534.
  * Specify your VM's static IP and port 8080 as the destination.
* In your IPFire interface you should be able to discover what IP address the firewall sees as your "public" IP address. Under VMware this will appear as a `192.168.x.x` address. **Make note of this address.**
* On your *main* PC (i.e. outside of VMware), open a browser and visit `http://<public-ip>:<port>`. 
  * For example, if you discovered that your router is at `192.168.55.100` and you forwarded port `8888`, you'd visit `http://192.168.55.100:8888`.
  * If everything worked, you should see the directory listing page in your browser *outside* of the VM!

Press Ctrl+C to stop the Python web server.

> In this context, accessing the VM from your main OS is akin to accessing the internal service through the firewall via the forwarded port. Since you are almost certainly behind a NAT to begin with, this will not actually open your server to the *actual* wider Internet. However, if you enacted this same setup on a router that is directly connected to the Internet - like your home router probably is - you *would* be opening access to the entire Internet.
>
> Be careful - exposing random services to the Internet can be dangerous!

## Deliverables

Once you've completed *all three* phases, perform another configuration backup as you did in Assignment 1. Additionally, take a screenshot of accessing the Python server from *outside* the VM (i.e. through the forwarded port). It should be clear that the screenshot is not from inside the VM - ensure the address bar is visible.

> Note that you should NOT undo any of the steps you took while performing the exercise. Leave the firewall rules and the DHCP assignment intact before saving your configuration.

Your deliverables are:

* The configuration backup after you've completed and tested all three phases
* The screenshot showing the forwarded port working
* Which service you decided to block - just type this in the text field in the D2L submission.

### Scoring Rubric

This assignment is worth 100 points. Points are assigned as follows:

| Item | Points | Penalties |
|-|-|-|
| Implement a static IP address for your client VM. | 35 | Point loss will occur if the static IP is not present, or if it is not outside of the DHCP pool. |
| Block a service by IP address. | 25 | Point loss if this task is not completed, or there is an error in how the firewall rule is configured. |
| Forward a port to a service running on the client VM. | 40 | Point loss if a screenshot is not included, if the configuration is incorrect, or if the task is not completed.
| 

Late submissions will receive a total loss of percentage of earned points based on the syllabus's Late Work policy. **Submissions 3 days late or later will receive 0 points.** See the [syllabus](SYLLABUS.md) for details on [late work submissions](SYLLABUS.md#deadlines).
