# Description
A personal website.  Generated using [Zola](https://getzola.org).

# Deploy Process
Currently, I perform manual deploys of this site using `git push production`, where
`production` is a remote pointing to a bare Git repo on my production server.  I am
planning to switch to a CI/CD workflow solution soon, but for now here is my process:

Begin by installing and configuring the Apache HTTP Server as needed.  The generated
website source will be placed in `/var/www/html`, so ensure that Apache serves content
there under the [https://beijaflor.es](https://beijaflor.es) base URL.  

To ensure that members of the `www-admin` group can reload Apache, add the following
line to your `/etc/sudoers` file using `sudo visudo`:

```lang-text
# Allow members of group www-admin to reload Apache2 with no password
%www-data ALL=NOPASSWD: /usr/sbin/service apache2 reload, /usr/sbin/service apache2 force-reload
```

Your deploy user should be in the `www-admin` group.  `/var/www/html` must also
be modifiable by the user used for deploys so that the `post-receive` hook works
properly, so I recommend making that folder and its children owned by
`www-data:www-data` as well.

To setup the bare repo on the production server:

```bash
$ git init --bare ~/beijaflor.es.git
```

To provide the initial `post-receive` hook:

```bash
$ curl https://raw.githubusercontent.com/obeijaflor/beijaflor.es/master/hooks/post-receive >~/beijaflor.es.git/hooks/post-receive
$ chmod +x ~/beijaflor.es.git/hooks/post-receive
```

Note that the hook updates itself from here on after.  However, a side effect of this is
that changes to the hook will lag behind by a commit before they actually take effect --
one more reason to switch to a better deploy process.

On your local machine, to test:

```bash
$ git remote add production username@example.com:beijaflor.es.git
$ git push production
```

More steps may be needed to configure the production server -- these are just rough
notes for now since the whole process needs to be reworked in the first place.
