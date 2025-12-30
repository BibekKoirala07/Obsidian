### Origin
Origin = protocols + host + port
Suppose the origin is `https://api.example.com`. Here `https` is protocol and `api.example.com` is hot and `443` is port(which isn't visible but it is default for https). And yes `www` is also subdomain and later it became optional. It is upto website owner what to put as canonical domain(main) whether it is `example.com` or `www.example.com`. The web doesn't require `www` subdomain anymore as web service is assumed to be default. It required at past cuz in past we used to have `www.example.com`, `ftp.example.com`, `mail.example.com` and more. So services were different.

`.com` or `.io` or `.dev` are called Top level domain or last part of domain. It is top level because it is highest level. Like the address in internet starts from this TLD. Everything like `google.com` starts after that. They don't affect the speed at all. They work the same despite it not feeling right. However some new TLD like `.app` or `.dev` requires `https` and not `http`.

However use naked domain as it is better for marketing.

Here is amazing realization.
`https://media.nepal.gov.np` Here `gov` is main domain. and `nepal` is subdomain. Not other thing. It was huge realization at the time of finding it out. However for some website which have `.uk` as TLD, we need to have second level Domain. so `.co.uk` or `.gov.uk` is compulsory.

### IP Address
Unique address associated to every devices. IP defines the machines and PORT defines the service on that machine.

### SSH
It let us securely log to another computer over a network using IP Address.
