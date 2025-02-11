# AioHttp Integration

## Using the standard client

### option 1: using environment variables

First run: export `APP_NAME=foo`, `export LABEL=main`


``` py title="aiohttp_example_1.py"
import logging

from config.ext import AioHttpConfig

from aiohttp import web


logging.basicConfig(level=logging.DEBUG)
app = web.Application()
routes = web.RouteTableDef()


@routes.get('/')
def home(request):
    body="""
    <html>
      <body>
      <p>config-client | aiohttp integration</p>
      <p>sample endpoints</p>
      <ul>
        <li><a href="http://localhost:8080/config">/config</a></li>
        <li><a href="http://localhost:8080/info">/info</a></li>
      </ul>
      </body>
    </html>
    """
    return web.Response(text=body, content_type='text/html')


@routes.get('/info')
def consul(request):
    return web.json_response(dict(
        description=app['config'].get('info.description'),
        url=app['config']['info']['url']
    ))

@routes.get('/config')
def config(request):
    return web.json_response(
        dict(config=app['config'])
    )

AioHttpConfig(app)
app.add_routes(routes)
web.run_app(app)
```

### option 2: using custom client

``` py title="aiohttp_example_2.py"
import logging

from config import ConfigClient
from config.ext import AioHttpConfig

from aiohttp import web


logging.basicConfig(level=logging.DEBUG)
app = web.Application()
routes = web.RouteTableDef()

@routes.get('/')
def home(request):
    body="""
    <html>
      <body>
      <p>config-client | aiohttp integration</p>
      <p>sample endpoints</p>
      <ul>
        <li><a href="http://localhost:8080/config">/config</a></li>
        <li><a href="http://localhost:8080/info">/info</a></li>
      </ul>
      </body>
    </html>
    """
    return web.Response(text=body, content_type='text/html')


@routes.get('/info')
def consul(request):
    return web.json_response(dict(
        description=app['cloud_config'].get('info.description'),
        url=app['cloud_config']['info']['url']
    ))

@routes.get('/config')
def config(request):
    return web.json_response(
        dict(config=app['cloud_config'])
    )

AioHttpConfig(
    app,
    key='cloud_config',
    client=ConfigClient(app_name='foo', label='main')
)
app.add_routes(routes)
web.run_app(app)
```

### option 3: using custom settings

``` py title="aiohttp_example_3.py"
import logging
from config import ConfigClient
from config.ext import AioHttpConfig

from aiohttp import web


logging.basicConfig(level=logging.DEBUG)
app = web.Application()
routes = web.RouteTableDef()

@routes.get('/')
def home(request):
    body="""
    <html>
      <body>
      <p>config-client | aiohttp integration</p>
      <p>sample endpoints</p>
      <ul>
        <li><a href="http://localhost:8080/config">/config</a></li>
        <li><a href="http://localhost:8080/info">/info</a></li>
      </ul>
      </body>
    </html>
    """
    return web.Response(text=body, content_type='text/html')


@routes.get('/info')
def consul(request):
    return web.json_response(dict(
        description=app['cloud_config'].get('info.description'),
        url=app['cloud_config']['info']['url']
    ))

@routes.get('/config')
def config(request):
    return web.json_response(
        dict(config=app['cloud_config'])
    )

AioHttpConfig(
    app,
    key='cloud_config',
    client=ConfigClient(app_name='foo', label='main'),
    verify='/etc/ssl/certs/ca-certificates.crt'
)
app.add_routes(routes)
web.run_app(app)
```


## Using the CloudFoundry client

Set `APP_NAME` environment variable, for example.

For details, see: [https://config-client.amenezes.net/docs/1.-overview/#setup](https://config-client.amenezes.net/docs/1.-overview/#setup)

### option 1: using environment variables

``` py title="aiohttp_cf_example_1.py"
import logging

from config import CF
from config.ext import AioHttpConfig

from aiohttp import web


logging.basicConfig(level=logging.DEBUG)
app = web.Application()
routes = web.RouteTableDef()


@routes.get('/')
def home(request):
    return web.Response(text="Hello, world")


@routes.get('/consul')
def consul(request):
    return web.json_response(dict(
        consul_port=app['config'].get('spring.cloud.consul.port'),
        consul_host=app['config']['spring']['cloud']['consul']['host']
    ))

@routes.get('/config')
def config(request):
    return web.json_response(
        dict(config=app['config'])
    )

AioHttpConfig(app, client=CF())
app.add_routes(routes)
web.run_app(app)
```

### option 2: using custom client

``` py title="aiohttp_cf_example_2.py"
import logging

from config import CF, ConfiClient
from config.ext import AioHttpConfig

from aiohttp import web


logging.basicConfig(level=logging.DEBUG)
app = web.Application()
routes = web.RouteTableDef()


@routes.get('/')
def home(request):
    return web.Response(text="Hello, world")


@routes.get('/consul')
def consul(request):
    return web.json_response(dict(
        consul_port=app['config'].get('spring.cloud.consul.port'),
        consul_host=app['config']['spring']['cloud']['consul']['host']
    ))

@routes.get('/config')
def config(request):
    return web.json_response(
        dict(config=app['config'])
    )

AioHttpConfig(app, client=CF(client=ConfigClient(app_name='simpleweb000')))
app.add_routes(routes)
web.run_app(app)
```

### option 3: using custom settings

``` py title="aiohttp_cf_example_3.py"
import logging

from config import CF, ConfiClient
from config.ext import AioHttpConfig

from aiohttp import web


logging.basicConfig(level=logging.DEBUG)
app = web.Application()
routes = web.RouteTableDef()


@routes.get('/')
def home(request):
    return web.Response(text="Hello, world")


@routes.get('/consul')
def consul(request):
    return web.json_response(dict(
        consul_port=app['config'].get('spring.cloud.consul.port'),
        consul_host=app['config']['spring']['cloud']['consul']['host']
    ))

@routes.get('/config')
def config(request):
    return web.json_response(
        dict(config=app['config'])
    )

AioHttpConfig(
    app,
    client=CF(client=ConfigClient(app_name='simpleweb000')),
    verify='/etc/ssl/certs/ca-certificates.crt'
)
app.add_routes(routes)
web.run_app(app)
```
