## Domain Name System

_Heart of modern network discovery_

<!-- vertical -->

### DNS explained

https://www.youtube.com/watch?v=72snZctFFtA

<iframe width="560" height="315" src="https://www.youtube.com/embed/72snZctFFtA" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<!-- vertical -->

### TLD and root server

- Root servers delegates to **T**op **L**evel **D**omain nameservers (.de, .com, ...)
- There are `"13"` root server to serve the whole internet
- Actually there are more than `13` server, routed by `Anycast IP` for distribution and latency improvement
- The ICANN (Internet Corporation for Assigned Names and Numbers”) organization manages all TLDs
- `.` indicates the "root" for all domains, usually magically added by the OS

<!-- vertical -->

### AS24 domain registration

- All domain registrations have to be managed by legal team
- `LEMARIT` operates all DNS related changes (registration, redirects for SEO domains, DNS delegation)
- `LEMARIT` delegates our main domains to Route53 (as24-dns) so we can manage records.
- Existence of rare cases of registrations through AWS Route53

<!-- vertical -->

### Zone record: Start of Authority (SOA)

_Every DNS zone requires a SOA record_

Mostly important for secondary nameservers (replicas)

```bind
;     SOA <primary master name server> <admin E-Mail, dot becomes = noc@dns.icann.org)
@ IN  SOA ns.icann.org. noc.dns.icann.org. (
          2020080302  ;Serial, indicates updates of a zone, typically timestamp
          7200        ;Refresh to detect changes for secondary name server
          3600        ;Retry for secondary name server
          1209600     ;Expire for secondary nameservers
          3600        ;Negative response caching TTL (cache not found for clients)
)
```

<!-- vertical -->

### Hands on

_Common CLI tools_

- nslookup
- host (simple lookup tool)
- dig (`brew install bind`)
- whois (display registration info)

<!-- vertical -->

### TLD, root server, wtf do you mean?

```bash
# get all root server, those have usually hard coded Anycast IPs for DNS servers
dig .  NS #
dig NS  # systems and libs add automatically the ending dot (root)

# get de responsible nameservers
dig de.  NS

# get AS24 responsible nameservers
dig autoscout24.com.  NS
dig api.autoscout24.com. NS

# IPv4 address
dig www.autoscout24.com. A
# IPv6 (no result)
dig www.autoscout24.com. AAAA

# owner
whois autoscout24.com
```

<!-- section -->

## Things you should know

<!-- vertical -->

### CNAME

> A Canonical Name record (abbreviated as CNAME record) is a type of resource record in the Domain Name System (DNS) that maps one domain name (an alias) to another (the canonical name).

- It can be cached (the partial response)
- A CNAME can alias different record types (TXT, A, AAAA, etc..)
- You cannot mix CNAME with other record types (e.g. A + CNAME is not possible), it must be exclusively used for a domain
- You cannot set a CNAME at zone level (see above)

<!-- vertical -->

### AWS Route53 and the Alias record

> You can create alias records to route traffic to selected AWS resources

- Workaround the CNAME limitations
- Directly resolves to A/AAAA records
- DNS queries are **free** of charge

<!-- vertical -->

### Reverse DNS lookup

> Reverse DNS resolution (rDNS) is the querying technique of the Domain Name System (DNS) to determine the domain name associated with an IP address

- IPv4 only: `.in-addr.arpa.`
- IPv6 zone: `.ip6.arpa.`
- Uses `PTR` (pointer) records,
- Originally acronym for Advanced Research Projects Agency (ARPA)

Example with a cloudfront IP:

```bash
# cloudfront
dig +short -x $(dig www.autoscout24.de +short A | grep -E '^[0-9]' | head -n 1)
# ec2 instance
dig +short -x $(dig +short bastion.global.aws.autoscout24.com)
```

<!-- section -->

# Thank you!

**Richard Hillmann**

_Senior Platform Engineer_
