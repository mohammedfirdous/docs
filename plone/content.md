# What is Plone?

[Plone](https://plone.org) is a free and open source content management system built on top of the Zope application server.

%%LOGO%%

## Features

-	Images for Plone 5.x and Plone 4.x
-	Enable add-ons via environment variables
-	Choose between [Debian](https://www.debian.org/) or [Alpine](http://www.alpinelinux.org/) based images.

## Usage

### Start a single Plone instance

This will download and start the latest Plone 5 container, based on [Debian](https://www.debian.org/).

```console
$ docker run -p 8080:8080 %%IMAGE%%
```

This image includes `EXPOSE 8080` (the Plone port), so standard container linking will make it automatically available to the linked containers. Now you can add a Plone Site at http://localhost:8080 - default Zope user and password are `admin/admin`.

### Start Plone within a ZEO cluster

ZEO cluster are best suited for production setups, you will **need** a loadbalancer.

Start ZEO server in the background

```console
$ docker run --name=zeo %%IMAGE%% zeo
```

Start 2 Plone clients (also in the background)

```console
$ docker run --link=zeo -e ZEO_ADDRESS=zeo:8080 -p 8081:8080 %%IMAGE%%
$ docker run --link=zeo -e ZEO_ADDRESS=zeo:8080 -p 8082:8080 %%IMAGE%%
```

### Start Plone in debug mode

You can also start Plone in debug mode (`fg`) by running

```console
$ docker run -p 8080:8080 %%IMAGE%% fg
```

### Add-ons

You can enable Plone add-ons via the `ADDONS` environment variable

```console
$ docker run -p 8080:8080 -e PLONE_ADDONS="eea.facetednavigation Products.PloneFormGen" %%IMAGE%%
```

For more information on how to extend this image with your own custom settings, adding more add-ons, building it or mounting volumes, please refer to our [documentation](https://docs.plone.org/manage/docker/docs/index.html)

## Supported Environment Variables

The Plone image uses several environment variable that allow to specify a more specific setup.

### For Basic Usage

-	`ADDONS` - Customize Plone via Plone add-ons using this environment variable
-	`ZEO_ADDRESS` - This environment variable allows you to run Plone image as a ZEO client.

Run Plone with ZEO and install two addons (PloneFormGen and collective.roster)

```console
$ docker run --name=instance1 --link=zeo -e ZEO_ADDRESS=zeo:8080 -p 8080:8080 \
-e ADDONS="Products.PloneFormGen collective.roster" plone
```

To use specific add-ons versions:

```console
 -e ADDONS="Products.PloneFormGen==1.8.5 collective.roster==2.3.1"
```

### For Advanced Usage

-	`PLONE_ZCML`, `ZCML` - Include custom Plone add-ons ZCML files (former `BUILDOUT_ZCML`)
-	`PLONE_DEVELOP`, `DEVELOP` - Develop new or existing Plone add-ons (former `BUILDOUT_DEVELOP`)
-	`ZEO_READ_ONLY` - Run Plone as a read-only ZEO client. Defaults to `off`.
-	`ZEO_CLIENT_READ_ONLY_FALLBACK` - A flag indicating whether a read-only remote storage should be acceptable as a fallback when no writable storages are available. Defaults to `false`.
-	`ZEO_SHARED_BLOB_DIR` - Set this to on if the ZEO server and the instance have access to the same directory. Defaults to `off`.
-	`ZEO_STORAGE` - Set the storage number of the ZEO storage. Defaults to `1`.
-	`ZEO_CLIENT_CACHE_SIZE` - Set the size of the ZEO client cache. Defaults to `128MB`.
-	`ZEO_PACK_KEEP_OLD` - Can be set to false to disable the creation of `*.fs.old` files before the pack is run. Defaults to true.
-	`HEALTH_CHECK_TIMEOUT` - Time in seconds to wait until health check starts. Defaults to `1` second.
-	`HEALTH_CHECK_INTERVAL` - Interval in seconds to check that the Zope application is still healthy. Defaults to `1` second.

## Documentation

Full documentation for end users can be found online at [docs.plone.org](https://docs.plone.org/manage/docker/docs/usage/index.html)

## Credits

This docker image was originally financed by the [European Environment Agency](http://eea.europa.eu), an agency of the European Union.

Thanks to [Antonio De Marinis](https://github.com/demarant), [Sven Strack](https://github.com/svx) and [Alin Voinea](https://github.com/avoinea) for their preliminary work.
