-- Fill lights [fill_light]
-- by David G (kestral246@gmail.com)
-- 2019-09-19
--
-- Provides full and half brightness fill light nodes to help work around the limited range of Minetest's lamps.
--

local user_config = {}

local search_range = 30  -- Distance to search for lights.
local search_time = 60  -- How long to search for lights (sec).
local half_brightness_value = 9  -- How bright to make half lights.

minetest.register_privilege("fill_lights", {description = "Allow toggling visibility of fill lights."})

-- Initialize player's state when player joins
minetest.register_on_joinplayer(function(player)
	local pname = player:get_player_name()
	user_config[pname] = {
			hide_fill = 0,
			show_fill = 0,
	}
end)

-- Define four fill light nodes.
minetest.register_node("fill_light:full", {
	description = "Fill light-full",
	drawtype = "glasslike",
	tiles = {"fill_light_full.png"},
	walkable = false,
	paramtype = "light",
	sunlight_propagates = true,
	light_source = default.LIGHT_MAX,
	pointable = true,
	buildable_to = false,
	drops = "",
	groups = {cracky = 3, oddly_breakable_by_hand = 3, fill_visible = 1},
})

minetest.register_node("fill_light:hide_full", {
	description = "Hidden fill light-full",
	drawtype = "airlike",
	walkable = false,
	paramtype = "light",
	sunlight_propagates = true,
	light_source = default.LIGHT_MAX,
	pointable = false,
	buildable_to = true,
	drops = "",
	groups = {cracky = 3, oddly_breakable_by_hand = 3, fill_hidden = 1, not_in_creative_inventory = 1},
})

minetest.register_node("fill_light:half", {
	description = "Fill light-half",
	drawtype = "glasslike",
	tiles = {"fill_light_half.png"},
	walkable = false,
	paramtype = "light",
	sunlight_propagates = true,
	light_source = half_brightness_value,
	pointable = true,
	buildable_to = false,
	drops = "",
	groups = {cracky = 3, oddly_breakable_by_hand = 3, fill_visible = 1},
})

minetest.register_node("fill_light:hide_half", {
	description = "Hidden fill light-half",
	drawtype = "airlike",
	walkable = false,
	paramtype = "light",
	sunlight_propagates = true,
	light_source = half_brightness_value,
	pointable = false,
	buildable_to = true,
	drops = "",
	groups = {cracky = 3, oddly_breakable_by_hand = 3, fill_hidden = 1, not_in_creative_inventory = 1},
})

-- Chat command to hide nearby fill lights.
minetest.register_chatcommand("hidefill", {
		params = "",
		description = "Hide fill lights.",
		privs = {},
		func = function(name, param)
				if minetest.check_player_privs(name, "fill_lights") then
					user_config[name].hide_fill = search_time
					user_config[name].show_fill = 0
				end
		end,
})

-- Chat command to show nearby fill lights.
minetest.register_chatcommand("showfill", {
		params = "",
		description = "Reveal hidden fill lights.",
		privs = {},
		func = function(name, param)
				if minetest.check_player_privs(name, "fill_lights") then
					user_config[name].hide_fill = 0
					user_config[name].show_fill = search_time
				end
		end,
})

-- Decrement hide_fill and show_fill countdown timers.
minetest.register_globalstep(function(dtime)
	local players  = minetest.get_connected_players()
	for _,player in ipairs(players) do
		if minetest.check_player_privs(player, "fill_lights") then
			local pname = player:get_player_name()
			local hf = user_config[pname].hide_fill
			if hf > 0 then
				hf = hf - dtime
				if hf <= 0 then
					user_config[pname].hide_fill = 0
				else
					user_config[pname].hide_fill = hf
				end
			end
			local rf = user_config[pname].show_fill
			if rf > 0 then
				rf = rf - dtime
				if rf <= 0 then
					user_config[pname].show_fill = 0
				else
					user_config[pname].show_fill = rf
				end
			end
		end
	end
end)

-- Hide fill lights.
local hide_fill = function(player)
	local ppos = player:get_pos()
	local pos = minetest.find_node_near(ppos, search_range, "group:fill_visible")
	if pos then
		if not minetest.is_protected(pos, player) then
			local node = minetest.get_node(pos)
			if node.name == "fill_light:full" then
				minetest.set_node(pos, {name = "fill_light:hide_full"})
			elseif node.name == "fill_light:half" then
				minetest.set_node(pos, {name = "fill_light:hide_half"})
			end
		end
	end
end

-- Reveal hidden fill lights.
local show_fill = function(player)
	local ppos = player:get_pos()
	local pos = minetest.find_node_near(ppos, search_range, "group:fill_hidden")
	if pos then
		if not minetest.is_protected(pos, player) then
			local node = minetest.get_node(pos)
			if node.name == "fill_light:hide_full" then
				minetest.set_node(pos, {name = "fill_light:full"})
			elseif node.name == "fill_light:hide_half" then
				minetest.set_node(pos, {name = "fill_light:half"})
			end
		end
	end
end

-- Replace fill lamps with appropriate version.
minetest.register_globalstep(function(dtime)
	for _, player in ipairs(minetest.get_connected_players()) do
		if minetest.check_player_privs(player, "fill_lights") then
			if user_config[player:get_player_name()].hide_fill > 0 then
				hide_fill(player)
			elseif user_config[player:get_player_name()].show_fill > 0 then
				show_fill(player)
			end
		end
	end
end)
