# If you need to shut down

Your cloud.gov apps have already incurred costs for January, so you don't have
any immediate obligation to stop your apps, however you may want to stop them
and replace them with a static website if you can't provide the operations and maintenance (O&M) the applications requires under ATO

To replace your applications with a static site:

1. Prepare your new content. You may just want to clone or fork
   https://github.com/18F/cf-ex-staticfile and update index.html 
   (see attached)

1. Connect to cloud.gov with the Cloud Foundry CLI:

```
cf login --sso -a https://api.fr.cloud.gov
cf target -o (your_org) -s (your_space)
```
 
2. Stop your current application:

cf stop (your_app)

3. Push the static site. Change directory to the clone of `cf-ex-staticfile`, then:

```
cf push (your_app)
```

4. If you have custom `routes` you'll need to re-map those:

```
cf map-route (your_app) --hostname (custom-route)
```

## Example: Replace app with static content

In this example, I'm replacing Java app `spring-music` at the hostname
`spring-music.app.cloud.gov` with the static site from
`cf-ex-staticfile`. It assumes you've already done a `cf login --sso`:

First stop the current app:

```
$ cf apps
Getting apps in org test-org / space dev as peter.burkholder@gsa.gov...
OK

name               requested state   instances   memory   disk   urls
landing            stopped           0/2         64M      1G
landing-test       stopped           0/2         64M      1G
preview-shutdown   stopped           0/1         512M     1G     preview-shutdown.app.cloud.gov
spring-music       started           1/1         1G       1G     spring-music.app.cloud.gov


$ cf stop spring-music
Stopping app spring-music in org test-org / space dev as peter.burkholder@gsa.gov...
OK
```

Now, change directory to the static site and `push` that out with the same name:

```
$ cd ~/path/to/cf-ex-staticfile

$ cf push spring-music
Updating app spring-music in org test-org / space dev as peter.burkholder@gsa.gov...
OK
...[snip]...
requested state: started
instances: 1/1
usage: 1G x 1 instances
urls: spring-music.app.cloud.gov
last uploaded: Fri Jan 19 20:10:44 UTC 2018
stack: cflinuxfs2
buildpack: staticfile
     state     since                    cpu    memory       disk         details
#0   running   2018-01-19 03:11:03 PM   0.0%   5.1M of 1G   7.2M of 1G
```

Now test at https://spring-music.app.cloud.gov

## Example route map
 
In this example, I want `landing-preview.app.cloud.gov` to also go to the same
static site from above:

```
$ cf map-route spring-music app.cloud.gov --hostname landing-preview
Creating route landing-preview.app.cloud.gov for org test-org / space dev as peter.burkholder@gsa.gov...
OK
Route landing-preview.app.cloud.gov already exists
Adding route landing-preview.app.cloud.gov to app spring-music in org test-org / space dev as peter.burkholder@gsa.gov...
OK
```

Now, I can vist https://landing-preview.app.cloud.gov and see the same site.






