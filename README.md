--// Services
local Players      = game:GetService("Players")
local StarterGui   = game:GetService("StarterGui")
local RunService   = game:GetService("RunService")

local player = Players.LocalPlayer

--// Your custom animations
local customAnimations = {
    Stand = "rbxassetid://74738520664045",
    Walk  = "rbxassetid://74738520664045",
    Run   = "rbxassetid://74738520664045"
}

local currentAnimationTrack = nil
local humanoid

--------------------------------------------------------------------
-- 1. Animation handling
--------------------------------------------------------------------
local function playAnimation(id)
    if currentAnimationTrack then
        currentAnimationTrack:Stop()
        currentAnimationTrack:Destroy()
    end

    if humanoid and id then
        local anim = Instance.new("Animation")
        anim.AnimationId = id
        currentAnimationTrack = humanoid:LoadAnimation(anim)
        currentAnimationTrack:Play()
    end
end

local function onHumanoidStateChanged()
    if not humanoid then return end

    local speed = humanoid.MoveDirection.Magnitude

    if speed > 8 then
        if not currentAnimationTrack or currentAnimationTrack.Animation.AnimationId ~= customAnimations.Run then
            playAnimation(customAnimations.Run)
        end
    elseif speed > 0.1 then
        if not currentAnimationTrack or currentAnimationTrack.Animation.AnimationId ~= customAnimations.Walk then
            playAnimation(customAnimations.Walk)
        end
    else
        if not currentAnimationTrack or currentAnimationTrack.Animation.AnimationId ~= customAnimations.Stand then
            playAnimation(customAnimations.Stand)
        end
    end
end

--------------------------------------------------------------------
-- 2. Character setup
--------------------------------------------------------------------
local function setupCharacter(char)
    humanoid = char:WaitForChild("Humanoid")

    -- Connect the per-frame update (only once per character)
    RunService.RenderStepped:Connect(onHumanoidStateChanged)

    -- Start with the stand animation
    playAnimation(customAnimations.Stand)
end

-- Existing character
if player.Character then
    setupCharacter(player.Character)
end

-- Respawn / new character
player.CharacterAdded:Connect(setupCharacter)

--------------------------------------------------------------------
-- 3. Notification
--------------------------------------------------------------------
-- The picture shows:
--   Title: "Fe Headless Head"
--   Text : "By Void_knght on YouTube"
--   Icon : the red Roblox character thumbnail
--   A green progress bar (handled automatically by Roblox)

local function sendCustomNotification()
    pcall(function()
        StarterGui:SetCore("SendNotification", {
            Title    = "Fe Headless Head",
            Text     = "By Void_knght on YouTube",
            Icon     = "rbxthumb://type=Avatar&id=" .. player.UserId .. "&w=150&h=150", -- red avatar thumbnail
            Duration = 5
        })
    end)
end

-- Show it once when the script runs (you can also tie it to an event)
sendCustomNotification()		_ENV.loadedFarm = nil
		_ENV.OnFarm = false
		
		local Message = Instance.new("Message", workspace)
		Message.Text = string.gsub(Text, urls.Owner, "")
		_ENV.rz_error_message = Message
		
		error(Text, 2)
	end
	
	local function formatUrl(Url)
		for string, path in urls do
			if Url:find("{" .. string .. "}") then
				return Url:gsub("{" .. string .. "}", path)
			end
		end
		
		return Url
	end
	
	function fetcher.get(Url)
		local success, response = pcall(function()
			return game:HttpGet(formatUrl(Url))
		end)
		
		if success then
			return response
		else
			CreateMessageError(`[1] [{ identifyexecutor() }] failed to get http/url/raw: { Url }\n>>{ response }<<`)
		end
	end
	
	function fetcher.load(Url, concat)
		local raw = fetcher.get(Url) .. (if concat then concat else "")
		local execute, error = loadstring(raw)
		
		if type(execute) ~= "function" then
			CreateMessageError(`[2] [{ identifyexecutor() }] sintax error: { Url }\n>>{ error }<<`)
		else
			return execute
		end
	end
end

local function IsPlace(Data)
	if Data.PlacesIds and table.find(Data.PlacesIds, game.PlaceId) then
		return true
	elseif Data.GameId and game.GameId == Data.GameId then
		return true
	end
	
	return false
end

for _, Data in Scripts do
	if IsPlace(Data) then
		return fetcher.load("{Repository}Games/" .. Data.Path)(fetcher, ...)
	end
end
