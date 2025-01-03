---
title: "Cloudflare Tunnels"
---

You can run Coolify on your local machine (like old laptop/Raspberry PI) and expose it to the internet without opening any ports on your router with Cloudflare Tunnels.

> For more details about CF Tunnels, please visit [this page](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/).

## Setup Cloudflared

You have at least two ways to setup Cloudflare Tunnels with Coolify.

- [Automated](#automated)
- [Manual](#manual)

### Automated

<Steps>
1. Setup Tunnels on Cloudflare
    1. Go to `https://one.dash.cloudflare.com/`.

    2. Select your account.

    3. Open `Networks`-> `Tunnels`-> `Create a Tunnel`

    4. Connector: `Cloudflared`
    ![connector](/images/cloudflare/cf-tunnels-connector.png)

    5. Choose any name you like.
    ![notice](/images/cloudflare/cf-tunnels-notice-me.png)

    6. Copy your `Cloudflare Tunnel Token` from any of the commands.
    <Aside type="caution">
    The token starts with `eyJ...`.
    </Aside>

    ![token](/images/cloudflare/cf-tunnels-token.png)

    7. On the `Route Tunnel` tab, add the following tunnels:

    <Aside type="caution">
    You can use any domains/subdomains. This will make sure you can reach your server through Cloudflare Tunnels.
    </Aside>

    ![ssh](/images/cloudflare/cf-tunnels-ssh.png)

2. Setup Tunnels on Coolify
    1. Add a new server with your server's `IP Address` - it will be reconfigured later on.
    ![addserver](/images/cloudflare/coolify-add-server.png)

    2. Validate the server.

    2. After the server is validated, click on `Configure` in the `Cloudflare Tunnels` section.

    3. Paste `Cloudflare Tunnel Token` from the previous step and set the `SSH Domain` to the domain you set in the previous step.
    ![setcftoken](/images/cloudflare/coolify-set-cf-token.png)

</Steps>

### Manual

WIP

## Setup Resources in Coolify

You have several options to use Cloudflare Tunnels with Coolify.

1. One domain -> One resource.
2. Wildcard subdomain -> All resources.

### One domain -> One resource

In this case, you need to add a public domain every time you would like to expose a new resource through Cloudflare Tunnels.

<Aside type="caution">
  You can stop `Coolify Proxy` and set it to `None`, it is not needed in this
  case.
</Aside>

1. Go to your tunnel settings on Cloudflare. (https://one.dash.cloudflare.com/ -> Networks -> Tunnels -> Select your tunnel)
2. Switch to `Public Hostname` tab.
3. Add a new `Public Hostname`.
   ![onepublic](/images/cloudflare/cf-one-public-hostname.png)
4. Go to Coolify and to your resource settings: - Remove any `Domains` settings. - Set `Port Mappings` to the same port that you set in the `Public Hostname` settings.
   <Aside type="caution">
     As an example, I'm deploying a static site, that listens in port `80`
     inside the container and I'm mapping it to the port `8888` on the host. So,
     I need to set the `Port Mappings` to `8888:80`.
     ![portmappings](/images/cloudflare/coolify-set-port-mappings.png)
   </Aside>
5. Deploy & enjoy.

### Wildcard subdomain -> All resources

In this case, you only need to setup a wildcard domain once and you can expose all your resources through it.

<Aside type="caution">
  You will need to use `Coolify's Proxy` to route the traffic to the correct
  resource.
</Aside>

1. Go to your tunnel settings on Cloudflare. (https://one.dash.cloudflare.com/ -> Networks -> Tunnels -> Select your tunnel)
2. Switch to `Public Hostname` tab.
3. Add a new wildcard `Public Hostname`.
   ![wildcard-cf](/images/cloudflare/cf-wildcard-public-hostname.png)
4. In Cloudflare go to ` Networks -> Tunnels` and click on your tunnel name. From the sidebar copy the `Tunnel ID`.
   ![cf-tunnel-id](/images/cloudflare/cf-tunnel-id.png)
5. In Cloudflare go to your `DNS` settings and add a new `CNAME` record with the following settings:
   - `Name`: `*`
   - `Target`: `<Tunnel ID>.cfargotunnel.com`
   - `TTL`: `Auto`
6. Go to Coolify and to your resource settings.

Set the `Domains` to any subdomain of the wildcard domain you set in the previous step.

![wildcard-coolify](/images/cloudflare/coolify-set-domains.png)

<Aside type="caution">
You need to use `http://` in the `Domains` settings. Cloudflare will take care of the `https` part.
For this you need to set `SSL/TLS` to `Full` in the `SSL/TLS` menu on Cloudflare.
![ssl-full](/images/cloudflare/cf-ssl-full.png)

</Aside>

7. Deploy & enjoy.

<Aside type="caution">
  If you would like to add a new resource, you only need to do point 6 and 7.
</Aside>

## Post Setup

After everything is setup, you can fully disable direct access to your server by disabling all the ports (except `SSH (port:22 by default)`) on your firewall.

## Setup self-hosted Coolify

You can use the [one domain](/docs/knowledge-base/cloudflare/tunnels/#one-domain---one-resource) without `Coolify Proxy` or [wildcard](/docs/knowledge-base/cloudflare/tunnels/#wildcard-subdomain---all-resources) setup with `Coolify Proxy` to expose your self-hosted Coolify instance to the internet.

With the `wildcard` setup, you have nothing to do.

With the `one domain` setup, you need a bit more setup with Coolify to make it work.

Let's say you configured the following `Public Hostnames` in Cloudflare:

- `app.coolify.io` mapped to `localhost:8000`
- `realtime.coolify.io` mapped to `localhost:6001`
- `terminal.coolify.io` mapped to `localhost:6002`

After you installed Coolify, you need to add 3 lines your `.env` file, located in `/data/coolify/source` folder.

```bash
APP_ID=<random string>
APP_KEY=<random string>
APP_NAME=Coolify
DB_PASSWORD=<random string>
PUSHER_APP_ID=<random string>
PUSHER_APP_KEY=<random string>
PUSHER_APP_SECRET=<random string>
REDIS_PASSWORD=<random string>

###########
# Add these lines
PUSHER_HOST=realtime.coolify.io
PUSHER_PORT=443
###########
```

This tells Coolify how to connect to it's realtime server through Cloudflare Tunnels.

Restart Coolify with the installation script.

```bash
curl -fsSL https://cdn.coollabs.io/coolify/install.sh | bash
```

> If you have a firewall, you also need to allow the [following ports](/docs/knowledge-base/server/firewall).

### Verify

1. Navigate to your Coolify instance, as in the example: `https://app.coolify.io`.
2. Login with the root user (the first user you created after installation).
3. Open another tab/window and navigate to `https://app.coolify.io/realtime`. On the other tab (opened in point 2), you should see a notification about the test event.
4. If you know what are you doing, you can check the network tab as well. Search for a websocket connection.
