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

