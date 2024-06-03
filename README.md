# DiscordOAuth2.py
Use Discord's OAuth2 effortlessly! Turns the auth code to a access token and the access token into scope infomation.

### Useful Links
Discord Server: https://discord.gg/DJ9xbbZAP5

Documentation is coming soon, don't worry.

## Quickstart
### Installing
I've finally published the library to PyPi! So now you can use pip.
```
pip install discord-oauth2.py
```
### Example With Flask
Don't forget to replace all the client information with your application's own information. You can leave bot token empty if your not adding members to guilds.
```py
import os
from flask import Flask, request, redirect

try:
  import discordoauth2
except(Exception):
  os.subprocess(['pip', 'install', 'discord-oauth2.py'])
  import discordoauth2

app = Flask(__name__)
client = discordoauth2.Client(1086836444535992422, secret=os.environ["CLIENT_SECRET"],
redirect="https://love-lounge-linked.treeben77.repl.co/linked-roles/support-team", bot_token=os.environ["CLIENT_TOKEN"])
app = Flask(__name__)

# client.update_linked_roles_metadata([
#   {
#       "type": 7,
#       "key": "guild_1086839511536898118",
#       "name": "Staff", # In Appeal Server
#       "description": "This person is in the appeal server."
#   },
#   {
#       "type": 7,
#       "key": "guild_1086839511536898118_owner",
#       "name": "Owner", # In Appeal Server
#       "description": "This person is the owner of the appeal server."
#   }
# ])

@app.route("/linked-roles/support-team")
def linkedroles_supportteam():
  code = request.args.get("code")
  if not code: return redirect("https://discord.com/oauth2/authorize?client_id=1086836444535992422&scope=role_connections.write+guilds&response_type=code&redirect_uri=https://love-lounge-linked.treeben77.repl.co/linked-roles/support-team")
    
  access = client.exchange_code(code)

  guilds = access.fetch_guilds()
  guild_ids = [guild["id"] for guild in guilds]
  
  guild_1086839511536898118_owner = False
  for guild in guilds:
    if guild["id"] == "1086839511536898118":
      guild_1086839511536898118_owner = guild["owner"]
      break
  
  access.update_metadata("Love Lounge",
    guild_1086839511536898118="1086839511536898118" in guild_ids,
    guild_1086839511536898118_owner=guild_1086839511536898118_owner)
  
  return "upated your metadata!"

app.run("0.0.0.0", 9066)
```
