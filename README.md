# mysteriousbot

A Discord bot that mainly sits in the
[Aurora Dynamics](https://auroradynamics.space/) server and assists in
trolling Peeky.

## Running

Running the bot yourself requires a few setup steps.

1. Create a Discord App for your bot at [discord.com][discord-app].
1. In that app under the "bot" tab, create a bot. You can name it and
   give it a picture, but for testing purposes this is unnecessary.
1. Add it to a Discord server, either one you already control or create
   a new bot playground server for testing. To add it to your server you
   will need the client id of the app you just created, which is located
   in the OAuth2 section of your app's configuration panel. Plop the id
   into a URL formatted like the following.

       https://discord.com/api/oauth2/authorize?client_id=<<CLIENT_ID>>&scope=bot&permissions=268552256

   Visit that URL in your browser, you should have a list of servers
   that you can add your bot to. If you do not see any servers, either
   you have already added the bot or you don't have the permission to
   add it to the server you want to add it to.
1. Now that you have the bot set up in Discord's API, you can run the
   bot locally, which connects to Discord's servers. Running the bot
   requires a *token* which is issued by the Discord server at the app
   page from before, under the bot page. Keep this token safe, it gives
   anyone who has it the ability to connect as your bot!

       # Linux and Mac
       DISCORD_TOKEN='' cargo run
       # Windows PowerShell
       $env:DISCORD_TOKEN = ''
       cargo run

## Deploying

Put the built program on a machine and run it. If you're patching it for
A0RA use, then the process of deploying is very complicated: poke
Necrothitude until he does it (he hasn't set up automation because
things seldom change).

If you're, for some reason, doing this yourself, these are
Necrothitude's notes on the matter. They may be of some help for you!

Prepare the server

```sh
ssh mysteriouspants.com:
    # create user
    sudo adduser --disabled-password mysteriousbot
    # enable ssh for this user - this is how we rsync new releases up
    sudo mkdir -p ~mysteriousbot/.ssh
    sudo cp ~/.ssh/authorized_keys  ~mysteriousbot/.ssh/authorized_keys
    sudo chown mysteriousbot:mysteriousbot ~mysteriousbot/.ssh/authorized_keys
    sudo vim /etc/systemd/system/mysteriousbot.service
```

The service file ought to look like this:

```
[Unit]
Description=Discord Chat Bot
After=network.target

[Service]
type=simple
User=cmdr
WorkingDirectory=/home/mysteriousbot
Environment="DISCORD_TOKEN=YOUR DISCORD TOKEN HERE"
ExecStart=/home/mysteriousbot/mysteriousbot
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Finally, run `deploy.sh` locally to build a release build and rsync the
results up, and kick the service on. You should mark the service to
start on boot as well. Because wild reboots happen.

```sh
local:
    ./deploy.sh
ssh mysteriouspants.com:
    sudo systemctl enable mysteriousbot
```

## License

We want you to be able to use this software regardless of who you may
be, what you are working on, or the environment in which you are working
on it - we hope you'll use it for good and not evil! To this end, the
Mysterious Discord Bot source code is licensed under the
[2-clause BSD][2cbsd] license, with other licenses available by request.
Happy coding!

[2cbsd]: https://opensource.org/licenses/BSD-2-Clause
[discord-app]: https://discord.com/developers/applications
