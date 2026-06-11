# Dinomight home lab
Set of docker compose stacks and other configuration for the home lab.

## Setup sequence

### Clone the repository
```bash
git clone https://github.com/dinomight/homelab.git /opt/homelab
```

### Create the docker proxy network
```bash
docker network create proxy
```

### Setup Treafik

#### Local files setup.
Create the local `acme.json` file so the docker container can find it and so it persists.
```bash
cd homelab/traefik
touch acme.json
chmod 600 acme.json
```

#### ACME email
The ACME email can be anything. Let's Encrypt will use this email to notify of certificate expiring, so it should be an
email that is checked.

#### Dashboard authentication
Create the `.env` file and populate it. Start by copying `.env.example`. To generate the `TRAEFIK_DASH_HASH`, use the
`httpd` docker image to generate it.
```bash
docker run --rm -it --entrypoint htpasswd httpd:2 -Bn admin
```

After running the command, the output will look like this.
```txt
New password: ••••••••
Re-type new password: ••••••••
admin:$2y$05$8IpPEG94/u.gX4Hn9zDU3...
```

Copy the `admin` line and paste it as the value for `TRAEFIK_DASH_HASH`.

#### DNSimple OAuth token
1. Log into DNSimple
2. Go to Account -> API Tokens
3. Click Generate New Token
4. Configure it:

    Name: traefik-homelab (or whatever you like)

5. Click Generate
6. Copy the token immediately - it's shown only once

Ideally, the token should be scoped, but only if paying for a tier that allows for it. Adjust instructions if the
account type allows for scoped tokens. It is also possible to delegate the DNS challenge to another provider like
Cloudflare or deSEC to remove DNSimple OAuth token. As it stands, the OAuth token is only on the docker host and has
limited exposure.

#### Domain
Domain is the just the domain all the services will be hosted under. It is what the DNS challenge is done against and
what all the certs will be generated for.
