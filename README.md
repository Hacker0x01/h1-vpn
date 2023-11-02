# h1-vpn

Docker image to connect to the HackerOne Gateway (VPN) through a socks5 proxy.

## Prerequisites 

1. In order to run this container you'll need docker installed.
    - [Windows](https://docs.docker.com/windows/started)
    - [OS X](https://docs.docker.com/mac/started/)
    - [Linux](https://docs.docker.com/linux/started/)

2. For Apple Silicon Mac, enable docker's [Rosetta for x86/amd64 emulation](https://docs.docker.com/desktop/settings/mac/). 

## Features

* Register a new Cloudflare WARP account by connecting to a HackerOne program
* Connect/disconnect from VPN
* Use socks5 proxy with optional authentication on port `1080`

## Usage

### Environment Variables

You can set username and password to connect to the proxy: `PROXY_USER` and `PROXY_PASSWORD`. 

Leave these variables blank to skip authentication while connecting to the socks5 server. 

### Steps

1. Start the container:

     ```
     PROXY_USER=<PROXY_USER> PROXY_PASSWORD=<PROXY_PASSWORD> docker-compose up -d
     ```

2. Check the connection status:

     ```
     docker-compose exec -it warp-cli warp-cli --accept-tos status
     ```

     If you see an error `Error: "Unable to connect to CloudflareWARP daemon. Maybe the daemon is not running?"`, run the following command to start CloudflareWARP daemon.

     ```
     docker-compose exec -it warp-cli warp-svc &
     ``` 

3. Register to a HackerOne program. Find the team-domain on your [Gateway Programs page](https://hackerone.com/settings/gateway).

     ```
     docker-compose exec -it warp-cli warp-cli --accept-tos teams-enroll <team-domain>
     ```

     Follow [this documentation](https://docs.hackerone.com/hackers/warp-client-config.html#linux) to obtain the token.

     ```
     docker-compose exec -it warp-cli warp-cli --accept-tos teams-enroll-token <token>
      ```

4. Once you are registered for a program, you can connect and disconnect from the VPN with one line of command. Connect to the VPN:

     ```
     docker-compose exec -it warp-cli warp-cli --accept-tos connect
      ```

5. Check the connection status:

     ```
     docker-compose exec -it warp-cli warp-cli --accept-tos status
   
     Status update: Connected
     Success
     ```

6. Check the account details. Make sure that Account type is `Team` and Organization shows the intended team domain.

     ```
     docker-compose exec -it warp-cli warp-cli account

     Account type: Team
     Device ID: <device_id>
     Public key: <public_key>
     Account ID: <account_id>
     Organization: <team-domain>
     ```

7. Test request example given `PROXY_USER=admin` and `PROXY_PASSWORD=admin`:

     ```
     curl --socks5-hostname admin:admin@localhost:1080 -k https://ifconfig.me
     ```

8. Disconnect from the VPN:

     ```
     docker-compose exec -it warp-cli warp-cli --accept-tos disconnect
     ```


