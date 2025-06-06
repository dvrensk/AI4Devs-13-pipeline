# David's prompts

## Prompt 1

I want to perform a scan of all files and technologies used in @backend  , as well as a list of dependencies and/or libraries, and finally business model concepts.

## Prompt 2

Document the project in /backend/docs

## Prompt 3

I want to serve the @backend  project from an AWS server running ubuntu. I've heard good things about PM2 but I'm equally interested in running docker. I'm familiar with nginx but not with acme. For the moment, I would be happy to run the DB server on the same host, either natively or with docker. I want to deploy with CI/CD on github, iff the tests pass. I want to use TLS and I have a domain name that I can point where I want, but I also want to be able to shut down the server when I don't use it, and I assume that I will lose the IP number then.
Please outline 4 or 5 options.

## Prompt 4

Option 2 sounds fine. Please create a file `/backend/deploy-todo.md` and create a list of things that we need to do according to this plan. Assume that I have and AWS account but no server.

## Prompt 5

I've extraced some files from the todo list. Please study @deploy to see the new layout.
Once you've done that, please change the notes to use an RDS database instead of something local. The endpoint is backend-1.chgeoqqmsntl.eu-north-1.rds.amazonaws.com.

## Prompt 6

@backend So far this repo has only been used in development. There are hardcoded values, both for DB URL and CORS, and maybe other things. Please find these places and propose 1 or 2 solutions for storing these values in a way that works both in development and production. Note that no production values (DB url, etc) will be stored in files in the repo, and that deploy will probably be performed through a git-pull, so make sure that there are no collisions. 

## Prompt 7

@backend So far this repo has only been used in development. There are hardcoded values, both for DB URL and CORS, and maybe other things. Please find these places and propose 1 or 2 solutions for storing these values in a way that works both in development and production. A few things to note:
* no production values (DB url, etc) will be stored in files in the repo
* deploy will probably be performed through a git-pull, so make sure that there are no collisions
* we use a dockerised DB in development, and @docker-compose.yml needs some data in the environment, so the solution must work for both docker and node.

--- FAILED

@backend  So far this repo has only been used in development. There are hardcoded values, both for DB URL and CORS, and maybe other things. Please find these places and extract the important values to `.env.development` and make sure that these values are used. When testing, it should read `.env.test`, and in production `.env.production`.

## Prompt 8

npx prisma fails. how to fix?

## Prompt 9

how do I migrate the test environment?

## Prompt 10

nope, still doesn't work

## Prompt 11

@deploy What is the next step?

## Prompt 12

I think we might have had a misunderstanding earlier. I talked about running the app with Docker, but what I meant was using Docker for nodejs etc, much like one would do in development. It seems like you took it to mean that I'd create a docker image in CI. Is that right?

## Prompt 13

Since we're only going to run this on one server now we can keep it simple. Can you rebuild the @deploy-todo.md assuming that we won't use docker, but instead install nodejs and traefik as ubuntu packages?
For the deploy I'm thinking something like Capistrano would do. Do you need a pointer or do you know what I mean?

## Prompt 14

I followed the instructions and installed nvm and node as user ubuntu.  But the next step is to install pm2 as root, and that fails since root doesn't have node.
I prefer to stay in userland and run pm2 as ubuntu. Is that compatible with best practice?

## Prompt 15

`save` complains:
ubuntu@ip-172-31-38-161:~$ pm2 save
[PM2] Spawning PM2 daemon with pm2_home=/home/ubuntu/.pm2
[PM2] PM2 Successfully daemonized
[PM2] Saving current process list...
[PM2][WARN] PM2 is not managing any process, skipping save...
[PM2][WARN] To force saving use: pm2 save --force

## Prompt 16

$ sudo apt install -y traefik
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package traefik

_Add some back-and-forth and now we install it through a tarball as befits a golang project._

## Prompt 17

How do I configure traefik?

## Prompt 18

Do we really want files in `/etc` to be owned by ubuntu?  Why?

## Prompt 19

Traefik will bind to ports 80 and 443, right? Doesn't it need to run as root? Or does it drop down to something else? If so, does it have to be to ubuntu?

## Prompt 20

$ sudo systemctl status traefik | cat
● traefik.service - Traefik Edge Router
     Loaded: loaded (/etc/systemd/system/traefik.service; enabled; preset: enabled)
     Active: active (running) since Mon 2025-05-19 08:41:37 UTC; 55s ago
       Docs: https://docs.traefik.io
   Main PID: 6369 (traefik)
      Tasks: 8 (limit: 1077)
     Memory: 25.7M (peak: 26.0M)
        CPU: 263ms
     CGroup: /system.slice/traefik.service
             └─6369 /usr/local/bin/traefik --configfile=/etc/traefik/traefik.yml

May 19 08:41:37 ip-172-31-38-161 traefik[6369]: Help us improve Traefik by turning this feature on :)
May 19 08:41:37 ip-172-31-38-161 traefik[6369]: More details on: https://doc.traefik.io/traefik/contributing/data-collection/
May 19 08:41:37 ip-172-31-38-161 traefik[6369]: 
May 19 08:41:37 ip-172-31-38-161 traefik[6369]: 2025-05-19T08:41:37Z ERR The ACME resolve is skipped from the resolvers list error="unable to get ACME account: permissions 640 for /etc/traefik/acme.json are too open, please use 600" resolver=letsencrypt

## Prompt 21

Yes, explain how traefik can write to that file given this service defnition

## Prompt 22

The logs show that it's trying to get a cert. How long should it take?
```
May 19 08:45:52 ip-172-31-38-161 traefik[6423]: 2025-05-19T08:45:52Z INF Testing certificate renew... acmeCA=https://acme-v02.api.letsencrypt.org/directory providerName=letsencrypt.acme
```

## Prompt 23

It seems to only listen on ipv6:
```
sudo netstat -tulpn | grep :80
tcp6       0      0 :::80                   :::*                    LISTEN      6423/traefik   
```

## Prompt 24

Something has moved!
```
May 19 08:56:53 ip-172-31-38-161 traefik[6952]: 2025-05-19T08:56:53Z INF Testing certificate renew... acmeCA=https://acme-v02.api.letsencrypt.org/directory providerName=letsencrypt.acme
May 19 08:56:53 ip-172-31-38-161 traefik[6952]: 2025-05-19T08:56:53Z INF Starting provider *acme.ChallengeTLSALPN
May 19 08:59:04 ip-172-31-38-161 traefik[6952]: 2025-05-19T08:59:04Z ERR Unable to get token error="missing token" providerName=acme
```

## Prompt 25

It didn't like that:
```
May 19 09:03:23 ip-172-31-38-161 systemd[1]: Started traefik.service - Traefik Edge Router.
May 19 09:03:23 ip-172-31-38-161 traefik[6994]: {"level":"error","error":"command traefik error: field not found, node: entryPoint","time":"2025-05-19T09:03:23Z","message":"Command error"}
May 19 09:03:23 ip-172-31-38-161 systemd[1]: traefik.service: Main process exited, code=exited, status=1/FAILURE
May 19 09:03:23 ip-172-31-38-161 systemd[1]: traefik.service: Failed with result 'exit-code'.
May 19 09:03:28 ip-172-31-38-161 systemd[1]: traefik.service: Scheduled restart job, restart counter is at 1.
May 19 09:03:28 ip-172-31-38-161 systemd[1]: Started traefik.service - Traefik Edge Router.
```

## Prompt 26

I'm about to start the app on the server.  If I run `pm2 start dist/index.js --name ai4devs-backend` how will it know to use NODE_ENV=production?

## Prompt 27

What if I add the variable to .bashrc? Or some other file like .profile?

## Prompt 28

I'm pretty sure setting RAILS_ENV=production in one of the dotfiles is what we've always been doing in the rails world. IIRC pm2 will be restarted by systemd if the machine reboots, right? Where does it pick up its environment?

## Prompt 29

Can you add this to the right section in the todo?

## Prompt 30

We still don't have the cert:
...
May 19 09:04:49 ip-172-31-38-161 traefik[7113]: 2025-05-19T09:04:49Z INF Testing certificate renew... acmeCA=https://acme-v02.api.letsencrypt.or>

## Prompt 31

The domain resolves, port 80 is open and redirects to 443 as expected. This is the debug output BEFORE disabling the challenge: [...]

## Prompt 32

It's time to give up traefik. Help me uninstall the service. Once we're done with that we'll add nginx instead, starting with just port 80, no TLS.

## Prompt 33

Great! pls update the todo with instructions for installing nginx

## Prompt 34

Now set up the github action to deploy the app to the server on push. (We'll add a check for no failing tests later.)
Before you start, describe what your plan is.

## Prompt 35

Please see here what I did to copy the shared files. Go ahead!

## Prompt 36

It failed. Last output was:
```
Cloning into '.'...
bash: line 1: npm: command not found
```
My guess is that this is because we're using nvm and it's not properly set up to work with non-interactive sessions.  How do we fix?

## Prompt 37

Let's not over-complicate things. I just move the nvm stuff to the top of the .bashrc and now it finds npm. But it looks like `npm run build` fails now:
```
Cloning into '.'...
added 132 packages, and audited 133 packages in 5s
15 packages are looking for funding
  run `npm fund` for details
6 vulnerabilities (3 low, 3 high)
To address all issues, run:
  npm audit fix
Run `npm audit` for details.
> backend@1.0.0 build
> tsc
sh: 1: tsc: not found
Error: Process completed with exit code 127.
```
Do you have any guess?

## Prompt 38

If that is required, why did it work when I did the first manual install?

## Prompt 39

I have access to the server and got this:
```
/var/www/ats_backend/releases/fda266ddc8cf73a46e7df8b91dd73924506b56d2/backend$ ls -l node_modules/.bin
total 0
lrwxrwxrwx 1 ubuntu ubuntu 25 May 19 12:21 js-yaml -> ../js-yaml/bin/js-yaml.js
lrwxrwxrwx 1 ubuntu ubuntu 14 May 19 12:21 mime -> ../mime/cli.js
lrwxrwxrwx 1 ubuntu ubuntu 20 May 19 12:21 mkdirp -> ../mkdirp/bin/cmd.js
lrwxrwxrwx 1 ubuntu ubuntu 24 May 19 12:21 prisma -> ../prisma/build/index.js
lrwxrwxrwx 1 ubuntu ubuntu 37 May 19 12:21 swagger-jsdoc -> ../swagger-jsdoc/bin/swagger-jsdoc.js
lrwxrwxrwx 1 ubuntu ubuntu 24 May 19 12:21 z-schema -> ../z-schema/bin/z-schema
```

## Prompt 40

I tried it, but it didn't run, since there was no change in the backend directory. Would it make sense to add the workflow directory to the triggers?

## Prompt 41

It works! Please add running the tests (npm test) as a prerequisite for deploying.

what is this step doing in the deploy section?

## Prompt 42

What are the steps to add LetsEncrypt to nginx?  If it's not too much work I'd be willing to give it a try.
