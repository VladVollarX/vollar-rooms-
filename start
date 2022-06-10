from discord.ext import commands

# You can set default values, if u want
default_rooms_initted = False
default_room_category_id = 969989485687357490
default_room_creator_id = 984686497548619838

room_category = None
room_creator = None

# https://discordpy.readthedocs.io/en/latest/api.html#discord.abc.GuildChannel.delete
async def delete_channel(guild, channel_id):
        channel = guild.get_channel(channel_id)
        await channel.delete()

# https://discordpy.readthedocs.io/en/latest/api.html#discord.Guild.create_voice_channel
async def create_voice_channel(guild, channel_name):
        channel = await guild.create_voice_channel(channel_name, category=room_category)
        return channel

def init_rooms():
    if default_room_category_id != -1:
        category_channel = client.get_channel(default_room_category_id)
        if category_channel:
            global room_category
            room_category = category_channel

    if default_room_creator_id != -1:
        create_channel = client.get_channel(default_room_creator_id)
        if create_channel:
            global room_creator
            room_creator = create_channel
  
    global default_rooms_initted
    default_rooms_initted = True

#https://discordpy.readthedocs.io/en/latest/api.html#discord.Guild.get_channel
@client.command(aliases = ['temp_category_set'])
async def __temp_category_set (ctx, id):
    category_channel = client.get_channel(int(id))
    if category_channel:
        global room_category
        room_category = category_channel

@client.command(aliases = ['temp_rooms_set'])
async def __temp_rooms_set (ctx, id):
    create_channel = client.get_channel(int(id))
    if create_channel:
        global room_creator
        room_creator = create_channel


# https://discordpy.readthedocs.io/en/latest/api.html#discord.on_voice_state_update
@client.event
async def on_voice_state_update(member, before, after):
    if not default_rooms_initted:
        init_rooms()

    if not room_category:
        print("Set 'Temp rooms category' id first (temp_category_set)")
        return False

    if not room_creator:
        print("Set 'Temp rooms creator' id first (temp_rooms_set)")
        return False

    if member.bot:
        return False
  
    # If user joined to the room creator channel
    if after.channel == room_creator:
        channel = await create_voice_channel(after.channel.guild, f'{member.name} room') # create new voice channel in temp rooms category
        if channel is not None: # if we successfully created our new voice room
            await member.move_to(channel) # move member to new room
            await channel.set_permissions(member, manage_channels=True) # set perm-s to the member
  
    # If user leaved temp room
    if before.channel is not None:
        if before.channel != room_creator and before.channel.category == room_category:
            if len(before.channel.members) == 0:
                await delete_channel(before.channel.guild, before.channel.id)