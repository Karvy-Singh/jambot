# Team Management Discord Bot

A Discord bot built for managing teams in hackathons and coding competitions. Handles everything from creating team roles to setting up private channels - basically automates all the boring admin stuff.

## What it does

### Team stuff
- Create teams with custom colored roles
- Track GitHub repos and usernames for each team
- Keep tabs on team status and member info
- Bulk import teams from your database
- Delete teams when you need to clean up

### The automation part
- Auto-creates private text & voice channels for every team
- Assigns Discord roles to team members in bulk
- Sets up channel permissions automatically
- One command to set up your entire hackathon infrastructure

### Communication tools
- Broadcast announcements to channels
- Create polls with emoji reactions
- Set reminders for deadlines
- View team listings and rosters

## Why this is perfect for hackathons

Here's how it typically works:
1. You collect registrations through Google Forms
2. Assign people to teams and export everything to Excel/CSV
3. Import that data into MongoDB (we'll show you how)
4. Run `/setup action:both` in Discord
5. The bot automatically creates all team roles, channels, and assigns everyone
6. Teams can start working in their private spaces immediately

Pretty straightforward.

## What you'll need

- Python 3.8 or higher
- MongoDB (local install or MongoDB Atlas for cloud)
- A Discord bot token
- Admin access to your Discord server

## Getting started

### Install everything
```bash
git clone <your-repo-url>
cd CodeJamv6
pip install -r requirements.txt
```

### Set up your environment
Create a `.env` file in the root directory:

```env
token=your_discord_bot_token
serverid=your_discord_server_id
mongouri=mongodb://localhost:27017/codejam
```

### Get your Discord bot token
1. Head to the [Discord Developer Portal](https://discord.com/developers/applications)
2. Create a new application (or pick an existing one)
3. Go to the Bot section
4. Hit Reset Token and copy it
5. Paste it in your `.env` file

### Get your server ID
1. Turn on Developer Mode in Discord (User Settings → Advanced → Developer Mode)
2. Right-click your server icon
3. Click Copy Server ID
4. Drop it in your `.env` file

### Invite the bot to your server
You'll need to generate an invite URL with:
- Scopes: `bot` and `applications.commands`
- Permissions: Administrator (or at minimum: Manage Roles, Manage Channels, Send Messages, Manage Messages, Add Reactions)

### Run it
```bash
python main.py
```

If you see `✓ Successfully synced X command(s) to guild`, you're good to go.

## Commands

### Team setup and management

**`/createteam`** - Create a new team with role and data all at once
- `name` - Team name (required)
- `color` - Color name or hex code like #ff6a00 (optional)
- `github_repo` - GitHub repository URL (optional)
- `github_usernames` - Comma-separated GitHub usernames (optional)
- `status` - Team status (optional)

Example:
```
/createteam name:Team Alpha color:blue github_repo:org/team-alpha status:Active
```

**`/updateteam`** - Update existing team info
- `team_name` - Team name (required)
- `github_repo` - Update GitHub repo (optional)
- `github_usernames` - Update GitHub usernames (optional)
- `status` - Update status (optional)

**`/deleteteam`** - Delete a team and all its data
- `team_name` - Team name to delete (required)

### Bulk operations

**`/setup`** - The big one. Does everything at once.
- `action` - Pick one: `channels`, `roles`, or `both` (required)

What each does:
- **channels** - Creates private text & voice channels for all teams
- **roles** - Assigns Discord roles to members based on your database
- **both** - Does everything (this is what you want for hackathons)

### Team info

**`/teaminfo`** - View team information
- `view` - Pick one: `specific`, `all`, or `members` (required)
- `team_name` - Team name (needed for specific/members view)

Views:
- **specific** - Shows detailed info for one team (repo, GitHub usernames, status, members)
- **all** - Lists all teams with summary info
- **members** - Shows who's in a specific team

### Member management

**`/manage`** - Add or remove people from teams
- `action` - Pick: `add` or `remove` (required)
- `team_name` - Team name (required)
- `member` - Discord member to add/remove (required)

Example:
```
/manage action:add team_name:Team Alpha member:@JohnDoe
```

### Communication

**`/announce`** - Send announcements
- `message` - Your announcement (required)
- `channels` - Comma-separated channel names, or leave empty for all channels (optional)

Example:
```
/announce message:Hackathon starts in 1 hour! channels:general,announcements
```

**`/poll`** - Create polls
- `question` - Poll question (required)
- `options` - Comma-separated options, 2-10 max (required)

Example:
```
/poll question:What time should we meet? options:9 AM, 10 AM, 11 AM
```

**`/reminder`** - Set reminders
- `message` - Reminder message (required)
- `time_minutes` - Time in minutes, 1-10080 max (that's 7 days) (required)

**`/help`** - Show all available commands
- No parameters needed - displays this help information

## Importing data from Excel/CSV

After your hackathon registration closes, you'll need to get that data into MongoDB. Here are a couple ways to do it:

### Using MongoDB Compass (the easy way)
1. Open MongoDB Compass and connect to your database
2. Select the `codejam` database
3. Import your CSV files to the `roles` and `team_members` collections

### Using mongoimport (command line)
```bash
mongoimport --db codejam --collection roles --type csv --headerline --file teams.csv
mongoimport --db codejam --collection team_members --type csv --headerline --file members.csv
```

### CSV format you'll need

**teams.csv:**
```csv
name,githubRepo,githubUsernames,status
Team Alpha,org/alpha-repo,"user1,user2",Active
Team Beta,org/beta-repo,"user3,user4",Active
```

**members.csv:**
```csv
team_name,discord_id,discord_username,discord_display_name
Team Alpha,123456789012345678,johndoe,John Doe
Team Alpha,234567890123456789,janedoe,Jane Doe
```

Pro tip: You can get Discord IDs by right-clicking users in Discord (just make sure Developer Mode is on).

## Who can run what

### Admin commands (need Administrator permission)
- `/createteam`, `/updateteam`, `/deleteteam`
- `/setup`, `/manage`
- `/announce`

### Anyone can use
- `/teaminfo`, `/poll`, `/reminder`

## Available colors

You can use color names like: red, blue, green, purple, orange, pink, gold, teal, magenta, light_blue, dark_blue, and a bunch more.

Or just use hex codes: `#ff6a00`, `#3498db`, etc.

## When things go wrong

The bot handles most errors gracefully:
- Checks permissions before doing anything
- Validates database connections
- Makes sure teams and members actually exist
- Won't let you spam commands
- Logs errors so you can figure out what happened

## Troubleshooting

**Bot not responding?**
1. Make sure it's actually online in Discord
2. Check if it has the right permissions
3. Look at the console for error messages
4. Try re-inviting the bot with the `applications.commands` scope

**Commands not showing up?**
```bash
# Just restart it
python main.py
```

**Can't connect to database?**
- Is MongoDB running?
- Check your `mongouri` in the `.env` file
- Try connecting manually: `mongosh "mongodb://localhost:27017/codejam"`

## License

MIT - use it however you want for your hackathons.

## Contributing

Found a bug or want to add something? Open an issue or send a PR.

---

Built for hackathon organizers who have better things to do than manually set up Discord channels.
