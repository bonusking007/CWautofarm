if KillAuraHitCooldown == nil then
    getgenv().KillAuraHitCooldown = 0.2
end

if SilentAimHitPart == nil then
    getgenv().SilentAimHitPart = "Head"
end

if is_sirhurt_closure then
    Players.LocalPlayer:Kick("imagine using sirhurt")
end


local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local mouse = Players.LocalPlayer:GetMouse()
local nevermore_modules = rawget(require(game.ReplicatedStorage.Framework.Nevermore), "_lookupTable")
local network = rawget(nevermore_modules, "Network") -- network is the place where the remote handling shit is
local remotes_table = getupvalue(getsenv(network).GetEventHandler, 1)
local events_table = getupvalue(getsenv(network).GetFunctionHandler, 1)
local remotes = {}
local lines = {}
local texts = {}
local players = {}
local boxes = {}
local boxoutlines = {}
local healthbars = {}
local healthbaroutlines = {}
local words = {
    "ez",
    "get good: get .gg/EzDK4AD5Yj",
    "trash",
    "touch grass",
    "retard",
    "i love among us",
    "the impostor?!?!?!",
    "grass? whats that",
    "having issues playing the game? get .gg/EzDK4AD5Yj",
    "is your dad spiderman? because he far from home",
    "do you ever have problems with light users parrying your ds???",
    "how are you that bad??🤣🤣😂🤣🤣",
    "EZ EZ EZ EZ EZ",
    "dont even bother insulting me 🤣🤣😂",
    "this script was brought to you by raid shadow legends!!",
    "do you like cheese?",
    "are you even trying to kill me???",
    "get rekt noobie",
    "go get .gg/EzDK4AD5Yj now",
    "imagine dying",
    ".gg/EzDK4AD5Yj on top (not really)",
    "L Bozo",
    "clapped",
    "nothing personel kid",
    "damn bro you got the whole squad laughing 😂😂🤣",
    "imagine targetting someone. but get clapped afterwards",
    "according to the rules. You should not be hacking because it can get you banned 🤓🤓🤓",
    "nerds be like: OMG LOOK AT THAT HACKER!!! LET'S GET HIM!!!🤓🤓🤓",
    "why am i still writing this? -Probably ZaneIs",
    "haha got you!!!",
    "how are you that bad??🤣😂",
    "нуб бозо",
    "my reaction to that information 😐",
    "OmG nO wAY a hACker!!!",
    "Super Idol的笑容",
    "goddamn i'm still writing -Probably ZaneIs",
    "have you ever heard the hitgame AmongUs???",
    "fr?",
    'skill issue',
    "touch grass losers",
    "this move is called 'Devious Lick'",
    "*Gorilla Sounds*",
    "What's up guys it's quandale dingle here.",
    "Bro got fake Jordans 💀",
    "frkfx is so cool",
    "Turi ip ip",
    "Say goodbye to your Kneecaps"
}


getgenv().hitremote = nil
getgenv().swingremote = nil
getgenv().fallremote = nil
getgenv().ragdollremote = nil

local hitpart = SilentAimHitPart
local ARROW
local bruh = Instance.new("Highlight",game.CoreGui)
local closest
local flying
local holdingm2 = false
local aimbotLocked
local retard
local shot = false
local weapon
local arrowsshooted = 1


-- will add all of these random lines into a config table later (maybe)
local Walkspeed = 16
local infjump
local antidamage
local autospawn
local tracersenabled
local nofall
local textenabled
local noclip
local stompaura
local jumppower = 50
local killsay = false
local killaura = false
local hidename = false
local aimbot
local silentaim
local autoequip = false
local nospread
local jumppowerenabled = false
local Walkspeedenabled = false
local silentaimhitchance = 100 -- in percents
local instantcharge = false
local boxesenabled = false

getgenv().TracerColor = Color3.fromRGB(99, 13, 197)


for i,v in pairs(getgc(true)) do
    if typeof(v) == "table" and rawget(v, "kick") then
        v.kick =  function()
            return
        end
    end

    if typeof(v) == 'table' and rawget(v, 'getIsBodyMoverCreatedByGame') then
        v.getIsBodyMoverCreatedByGame = function(among)
            return true
        end
   end
   if typeof(v) == "table" and rawget(v, "randomDelayKick") then
        v.randomDelayKick = function()
            return wait(9e9)
        end
    end
end

table.foreach(remotes_table, function(i,v)
    if rawget(v, "Remote") then
        remotes[rawget(v, "Remote")] = i
    end
end)

table.foreach(events_table, function(i,v)
    if rawget(v, "Remote") then
        remotes[rawget(v, "Remote")] = i
    end
end)



local pog
pog = hookmetamethod(game, "__index", function(self, key)
    if (key == "Name" or key == "name") and remotes[self] then
       return remotes[self]
    end

    return pog(self, key)
end)


local function getRemote(name)
    for i,v in pairs(remotes) do
        if i.Name == name then
            return i
        end
    end
end


local function getClosest()
    local hrp = Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").Position
    local closest_distance = math.huge
    local closestnigger

    for i,v in pairs(game.Players:GetPlayers()) do
        if v.Character ~= nil and v ~= Players.LocalPlayer and v.Character:FindFirstChild("HumanoidRootPart") ~= nil and v.Character:FindFirstChild("Humanoid").Health > 0 then
            local plr_pos = v.Character.HumanoidRootPart.Position
            local plr_distance = (hrp - plr_pos).Magnitude
    
            if plr_distance < closest_distance then
                closest_distance = plr_distance
                closestnigger = v
            end
        end
    end

    return closestnigger
end


local function getClosestToMouse()
    local player, nearestDistance = nil, math.huge
    for i,v in pairs(Players:GetPlayers()) do
        if v ~= Players.LocalPlayer and v.Character:FindFirstChild("Humanoid") and v.Character.Humanoid.Health > 0 and v.Character:FindFirstChild("HumanoidRootPart") then
            local root, visible = workspace.CurrentCamera:WorldToViewportPoint(v.Character.HumanoidRootPart.Position)
            if visible then
                local distance = (Vector2.new(mouse.X, mouse.Y) - Vector2.new(root.X, root.Y)).Magnitude

                if distance < nearestDistance then
                    nearestDistance = distance
                    player = v
                end
            end
        end
    end
    return player
end


local function calculateArrowHitChance(v)

    local chance = math.floor(Random.new().NextNumber(Random.new(),0,1) * 100) / 100
    return chance <= math.floor(v) / 100
end

FLYING = true
iyflyspeed = 1
vehicleflyspeed = 1

function sFLY(vfly)
    while wait() do
        repeat wait() until mouse
        if flyKeyDown or flyKeyUp then flyKeyDown:Disconnect() flyKeyUp:Disconnect() end
    
        local T = Players.LocalPlayer.Character.HumanoidRootPart
        local CONTROL = {F = 0, B = 0, L = 0, R = 0, Q = 0, E = 0}
        local lCONTROL = {F = 0, B = 0, L = 0, R = 0, Q = 0, E = 0}
        local SPEED = 0
    
        local function FLY()
            FLYING = true
            local BG = Instance.new('BodyGyro')
            local BV = Instance.new('BodyVelocity')
            BG.P = 9e4
            BG.Parent = T
            BV.Parent = T
            BG.maxTorque = Vector3.new(9e9, 9e9, 9e9)
            BG.cframe = T.CFrame
            BV.velocity = Vector3.new(0, 0, 0)
            BV.maxForce = Vector3.new(9e9, 9e9, 9e9)
            task.spawn(function()
                repeat wait()
                    if not vfly and Players.LocalPlayer.Character:FindFirstChildOfClass('Humanoid') then
                        Players.LocalPlayer.Character:FindFirstChildOfClass('Humanoid').PlatformStand = true
                    end
                    if CONTROL.L + CONTROL.R ~= 0 or CONTROL.F + CONTROL.B ~= 0 or CONTROL.Q + CONTROL.E ~= 0 then
                        SPEED = 50
                    elseif not (CONTROL.L + CONTROL.R ~= 0 or CONTROL.F + CONTROL.B ~= 0 or CONTROL.Q + CONTROL.E ~= 0) and SPEED ~= 0 then
                        SPEED = 0
                    end
                    if (CONTROL.L + CONTROL.R) ~= 0 or (CONTROL.F + CONTROL.B) ~= 0 or (CONTROL.Q + CONTROL.E) ~= 0 then
                        BV.velocity = ((workspace.CurrentCamera.CoordinateFrame.lookVector * (CONTROL.F + CONTROL.B)) + ((workspace.CurrentCamera.CoordinateFrame * CFrame.new(CONTROL.L + CONTROL.R, (CONTROL.F + CONTROL.B + CONTROL.Q + CONTROL.E) * 0.2, 0).p) - workspace.CurrentCamera.CoordinateFrame.p)) * SPEED
                        lCONTROL = {F = CONTROL.F, B = CONTROL.B, L = CONTROL.L, R = CONTROL.R}
                    elseif (CONTROL.L + CONTROL.R) == 0 and (CONTROL.F + CONTROL.B) == 0 and (CONTROL.Q + CONTROL.E) == 0 and SPEED ~= 0 then
                        BV.velocity = ((workspace.CurrentCamera.CoordinateFrame.lookVector * (lCONTROL.F + lCONTROL.B)) + ((workspace.CurrentCamera.CoordinateFrame * CFrame.new(lCONTROL.L + lCONTROL.R, (lCONTROL.F + lCONTROL.B + CONTROL.Q + CONTROL.E) * 0.2, 0).p) - workspace.CurrentCamera.CoordinateFrame.p)) * SPEED
                    else
                        BV.velocity = Vector3.new(0, 0, 0)
                    end
                    BG.cframe = workspace.CurrentCamera.CoordinateFrame
                until not FLYING
                CONTROL = {F = 0, B = 0, L = 0, R = 0, Q = 0, E = 0}
                lCONTROL = {F = 0, B = 0, L = 0, R = 0, Q = 0, E = 0}
                SPEED = 0
                BG:Destroy()
                BV:Destroy()
                if Players.LocalPlayer.Character:FindFirstChildOfClass('Humanoid') then
                    Players.LocalPlayer.Character:FindFirstChildOfClass('Humanoid').PlatformStand = false
                end
            end)
        end
        flyKeyDown = mouse.KeyDown:Connect(function(KEY)
            if KEY:lower() == 'w' then
                CONTROL.F = (vfly and vehicleflyspeed or iyflyspeed)
            elseif KEY:lower() == 's' then
                CONTROL.B = - (vfly and vehicleflyspeed or iyflyspeed)
            elseif KEY:lower() == 'a' then
                CONTROL.L = - (vfly and vehicleflyspeed or iyflyspeed)
            elseif KEY:lower() == 'd' then 
                CONTROL.R = (vfly and vehicleflyspeed or iyflyspeed)
            elseif QEfly and KEY:lower() == 'e' then
                CONTROL.Q = (vfly and vehicleflyspeed or iyflyspeed)*2
            elseif QEfly and KEY:lower() == 'q' then
                CONTROL.E = -(vfly and vehicleflyspeed or iyflyspeed)*2
            end
            pcall(function() workspace.CurrentCamera.CameraType = Enum.CameraType.Track end)
        end)
        flyKeyUp = mouse.KeyUp:Connect(function(KEY)
            if KEY:lower() == 'w' then
                CONTROL.F = 0
            elseif KEY:lower() == 's' then
                CONTROL.B = 0
            elseif KEY:lower() == 'a' then
                CONTROL.L = 0
            elseif KEY:lower() == 'd' then
                CONTROL.R = 0
            elseif KEY:lower() == 'e' then
                CONTROL.Q = 0
            elseif KEY:lower() == 'q' then
                CONTROL.E = 0
            end
        end)
        FLY()
    end
end

function NOFLY()
    FLYING = false
    if flyKeyDown or flyKeyUp then flyKeyDown:Disconnect() flyKeyUp:Disconnect() end
    if Players.LocalPlayer.Character:FindFirstChildOfClass('Humanoid') then
        Players.LocalPlayer.Character:FindFirstChildOfClass('Humanoid').PlatformStand = false
    end
    pcall(function() workspace.CurrentCamera.CameraType = Enum.CameraType.Custom end)
end


firehit = function(character,arrow)
    local fakepos = character[hitpart].Position + Vector3.new(math.random(1,5),math.random(1,5),math.random(1,5))
    local args = {
        [1] = Players.LocalPlayer.Character:FindFirstChildOfClass("Tool"),
        [2] = character.Head,
        [3] = fakepos,
        [4] = character.Head.CFrame:ToObjectSpace(CFrame.new(fakepos)),
        [5] = fakepos * Vector3.new(math.random(1,5),math.random(1,5),math.random(1,5)),
        [6] = tostring(arrowsshooted)
    }
    getRemote("RangedHit"):FireServer(unpack(args))
end

getgenv().hitremote = getRemote("MeleeDamage")
getgenv().swingremote = getRemote("MeleeSwing")
getgenv().fallremote = getRemote("TakeFallDamage")

for i,v in pairs(getgc(true)) do
    if typeof(v) == "table" and rawget(v, "connectCharacter") then
        v.connectCharacter = function(among)
            return
        end
    end
end


task.wait(0.5)

pcall(function()
    for i = 1,25 do
        getRemote("StartFastRespawn"):FireServer()
        getRemote("CompleteFastRespawn"):FireServer()
        wait()
    end
end)

local ui = loadstring(game:HttpGet('https://raw.githubusercontent.com/StepBroFurious/Script/main/HydraHubUi.lua'))()
local window = ui.new("frkfx#7420", Players.LocalPlayer.UserId, "ALPHA")
local main = window:Category("Rage", "rbxassetid://7733965386")
local player = window:Category("Player", "rbxassetid://7743875962")
local Combat = window:Category("Combat", "rbxassetid://7743878358")
local misc = window:Category("Misc", "rbxassetid://7734042071")
local ESP = window:Category("ESP", "rbxassetid://7733774602")

local mainMain = main:Button("Rage", "rbxassetid://7743875962")
local mainSection = mainMain:Section("Toggles", "Left")
local playerMain = player:Button("Player", "rbxassetid://7743875962")
local playerSection = playerMain:Section("Player", "Left")
local CombatMain = Combat:Button("Combat", "rbxassetid://7743875962")
local CombatKillauraSection = CombatMain:Section("Kill Aura", "Left")
local CombatSilentaimSection = CombatMain:Section("Silent Aim", "Right")
local miscMain = misc:Button("Misc", "rbxassetid://7743875962")
local miscSection = miscMain:Section("BROKEN", "Left")
local ESPMain = ESP:Button("ESP", "rbxassetid://7743875962")
local ESPSection = ESPMain:Section("ESP", "Left")

mainSection:Button({
    Title = "Emotes",
    ButtonName = "Unlock",
    },
    function(v)
        for i,v in pairs(getgc(true)) do
            if typeof(v) == "table" and rawget(v, "gamepassIdRequired") then
                if v.gamepassIdRequired ==  "danceEmotes" then
                    v.gamepassIdRequired = nil
                elseif v.gamepassIdRequired == "toxicEmotes" then
                    v.gamepassIdRequired = nil
                elseif v.gamepassIdRequired == "respectEmotes" then
                    v.gamepassIdRequired = nil
                end
            end
        end
    end
)

mainSection:Button({
    Title = "Auto Parry",
    ButtonName = "Enable",
    },
    function(v)

	local lp = game.Players.LocalPlayer

local animationInfo = {}

function getInfo(id)
  local success, info = pcall(function()
      return game:GetService("MarketplaceService"):GetProductInfo(id)
  end)
  if success then
      return info
  end
  return {Name=''}
end
function block(player)
  keypress(0x46)
  wait()
  keyrelease(0x46)
end

local AnimNames = {
  'Slash',
  'Swing',
  'Sword'
}

function playerAdded(v)
    local function charadded(char)
      local humanoid = char:WaitForChild("Humanoid", 5)
      if humanoid then
          humanoid.AnimationPlayed:Connect(function(track)
              local info = animationInfo[track.Animation.AnimationId]
              if not info then
                  info = getInfo(tonumber(track.Animation.AnimationId:match("%d+")))
                  animationInfo[track.Animation.AnimationId] = info
              end
             
              if (lp.Character and lp.Character:FindFirstChild("Head") and v.Character:FindFirstChild("Head")) then
                  local mag = (v.Character.Head.Position - lp.Character.Head.Position).Magnitude
                  if mag < 15  then
                     
                      for _, animName in pairs(AnimNames) do
                          if info.Name:match(animName) then
                              pcall(block, v)
                          end
                      end
                     
                  end
              end
          end)
      end
  end
 
  if v.Character then
      charadded(v.Character)
  end
  v.CharacterAdded:Connect(charadded)
end

for i,v in pairs(game.Players:GetPlayers()) do
   if v ~= lp then
       playerAdded(v)
   end
end

game.Players.PlayerAdded:Connect(playerAdded)
end)

mainSection:Toggle({
    Title = "Inf Parry",
    Default = false
    },
    function(val)
        for i,v in pairs(getgc(true)) do
            if type(v) == "table" and rawget(v, "PARRY_COOLDOWN_IN_SECONDS") and rawget(v, "PARRY_COOLDOWN_IN_SECONDS_AFTER_SUCCESSFUL_PARRY") then
                if val then
                    v.PARRY_COOLDOWN_IN_SECONDS = 0
                    v.PARRY_COOLDOWN_IN_SECONDS_AFTER_SUCCESSFUL_PARRY = 0
                else
                    v.PARRY_COOLDOWN_IN_SECONDS = 3
                    v.PARRY_COOLDOWN_IN_SECONDS_AFTER_SUCCESSFUL_PARRY = 0.33
                end
            end
        end
    end
)


mainSection:Toggle({
    Title = "Spam Jump",
    Default = false
    },
    function(val)
        for i,v in pairs(getgc(true)) do
            if typeof(v) == "table" and rawget(v, "getCanJump") then
                local old = v.getCanJump
                if val then
                    v.getCanJump = function()
                        return true
                    end
                else
                    return old()
                end
            end
        end
    end
)

mainSection:Toggle({
    Title = "Inf Stamina",
    Default = false
    },
    function(val)
        for i,v in pairs(getgc(true)) do
            if typeof(v) == "table" and rawget(v, "_setStamina") then
                local old = v._setStamina
                v._setStamina = function(among, us)
                    if val then
                        among._stamina = math.huge
                        among._staminaChangedSignal:Fire(150)
                    else
                        return old(among, us)
                    end
                end
            end
         end
    end
)

mainSection:Toggle({
    Title = "No Fall Damage",
    Default = false
    },
    function(val)
        nofall = val
    end
)

mainSection:Toggle({
    Title = "Stomp Aura",
    Default = false
    },
    function(val)
        stompaura = val
    end
)

mainSection:Toggle({
    Title = "No Dash Cooldown",
    Default = false
    },
    function(val)
        for i,v2 in pairs(getgc(true)) do
            if typeof(v2) == "table" and rawget(v2, "DASH_COOLDOWN") then
                if val then
                    v2.DASH_COOLDOWN = 0
                else
                    v2.DASH_COOLDOWN = 3

                end
            end
        end
    end
)

mainSection:Toggle({
    Title = "No Utility Damage",
    Default = false
    },
    function(val)
        antidamage = val
    end
)

mainSection:Toggle({
    Title = "Auto Spawn",
    Default = false
    },
    function(val)
        autoSpawn = val
    end
)

mainSection:Toggle({
    Title = "No Ragdoll",
    Default = false
    },
    function(val)
        for i,v in pairs(getgc(true)) do
            if typeof(v) == "table" and rawget(v, "toggleRagdoll") then
                local old = v.toggleRagdoll
                v.toggleRagdoll = function(among, us, irl)
                    if val then
                        return
                    else
                        return old(among, us, irl)
                    end
                end
            end
        end
    end
)

playerSection:Toggle({
    Title = "enabled",
    Default = false
    },
    function(val)
        walkspeedenabled = val

        if not val then
            Players.LocalPlayer.Character:WaitForChild("Humanoid").Walkspeed = 16
        end
    end
)

playerSection:Slider({
    Title = "Walkspeed",
    Description = "",
    Default = 16,
    Min = 16,
    Max = 75
    },
    function(v)
        walkspeed = v
    end
)

playerSection:Toggle({
    Title = "enabled",
    Default = false
    },
    function(val)
        jumppowerenabled = val

        if not val then
            Players.LocalPlayer.Character:WaitForChild("Humanoid").JumpPower = 50
        end
    end
)

playerSection:Slider({
    Title = "Jump Power",
    Description = "",
    Default = 50,
    Min = 50,
    Max = 200
    },
    function(v)
        jumppower = v
    end
)

playerSection:Toggle({
    Title = "Inf jump",
    Default = false
    },
    function(val)
        infjump = val
    end
)

playerSection:Toggle({
    Title = "Noclip",
    Default = false
    },
    function(val)
        noclip = val
    end
)

playerSection:Toggle({
    Title = "Killsay",
    Default = false
    },
    function(val)
        killsay = val
    end
)

playerSection:Toggle({
    Title = "Hide Name",
    Default = false
    },
    function(val)
        hidename = val
    end
)

playerSection:Toggle({
    Title = "Fly",
    Default = false
    },
    function(val)
        flying = not flying
        if val then
            sFLY(true)
        else
            NOFLY()
        end
    end
)

playerSection:Keybind({
    Title = "Fly Keybind",
    Default = Enum.KeyCode.K
    },
    function(val)
        flying = not flying

        if flying then
            sFLY(true)
        else
            NOFLY()
        end
    end
)

playerSection:Toggle({
    Title = "Auto Equip",
    Default = false
    },
    function(val)
        autoequip = val
    end
)

playerSection:Toggle({
    Title = "Jesus",
    Default = false
    },
    function(val)
        for i,v2 in pairs(game.Workspace.Map:GetDescendants()) do
            if v2.Name == "WaterArea" then
                if val then
                    v2.CanCollide = true
                else
                    v2.CanCollide = false
                end
            end
        end
    end
)

CombatKillauraSection:Toggle({
    Title = "Kill Aura",
    Default = false
    },
    function(val)
        killaura = val
    end
)

-- reach here

CombatKillauraSection:Toggle({
    Title = "Reach (broken)",
    Default = false
    },
    function(val)
        Reaching = val
        if val == false then return end
        local character = game.Players.LocalPlayer.Character
        local torso = character.Torso
        local rightarm = character["Right Arm"]
        local rightshoulder = torso["Right Shoulder"]
        if rightshoulder and rightshoulder.Enabled == true then
            local rightshoulderclone = rightshoulder:Clone()
            rightshoulderclone.Enabled = false
            rightshoulder:Destroy()
            rightshoulderclone.Parent = torso
        end
    
        for i,v in pairs(game.Players.LocalPlayer.Backpack:GetDescendants()) do
            if v:IsA("BasePart") then
                v.Massless = true
            end
        end
        if rightarm:FindFirstChild("RagdollBallSocket") then
            rightarm.RagdollBallSocket.Enabled = false
        end
        while Reaching do
            sethiddenproperty(rightarm,"AssemblyLinearVelocity",Vector3.new(100,100,100))
            rightarm.Velocity = Vector3.new(100,100,100)
            if torso:FindFirstChild("Right Shoulder") then
                local rightarm = character["Right Arm"]
                local rightshoulder = torso["Right Shoulder"]
    
                local rightshoulderclone = rightshoulder:Clone()
                rightshoulderclone.Enabled = false
                rightshoulder:Destroy()
                rightshoulderclone.Parent = torso
            end
    
            local target = getClosest()

            -- if reachchoice == "closest to player" then
            --     target = getClosest()
            -- elseif reachchoice == "closest to mouse" then
            --     target = getClosestToMouse()
            --     bruh.Adornee = target.Character
            -- else
            --     target = getClosest()
            -- end
            if target.Character:FindFirstChild("Head") and (character.Head.Position - target.Character.Head.Position).magnitude < 100 then
                rightarm.CFrame = target.Character.Head.CFrame * CFrame.new(math.random(-0,0),0,0)
            elseif character:FindFirstChild("Head") then
                rightarm.CFrame = character.Head.CFrame * CFrame.new(math.random(-0,0),0,0)
            end
            game:GetService("RunService").Heartbeat:wait()
        end
    end
)


CombatSilentaimSection:Toggle({
    Title = "Aimbot",
    Default = false
    },
    function(val)
        aimbot = val
    end
)

CombatSilentaimSection:Toggle({
    Title = "Silent Aim",
    Default = false
    },
    function(val)
        silentaim = val
    end
)

CombatSilentaimSection:Slider({
    Title = "Hit Chance",
    Description = "",
    Default = 100,
    Min = 0,
    Max = 100
    },
    function(v)
        silentaimhitchance = v
    end
)

CombatSilentaimSection:Toggle({
    Title = "Wallbang",
    Default = false
    },
    function(val)
        if val then
            game.CollectionService:AddTag(game:GetService("Workspace").Map,'RANGED_CASTER_IGNORE_LIST')
        else
            game.CollectionService:RemoveTag(game:GetService("Workspace").Map,'RANGED_CASTER_IGNORE_LIST')
        end
    end
)

CombatSilentaimSection:Toggle({
    Title = "No Spread",
    Default = false
    },
    function(val)
        nospread = val
    end
)

CombatSilentaimSection:Toggle({
    Title = "No Recoil",
    Default = false
    },
    function(val)
        for i,v2 in pairs(getgc(true)) do
            if typeof(v2) == "table" and rawget(v2, "recoilAmount") then
                if val then
                    v2.recoilAmount = 0
                    v2.recoilXMin = 0
                    v2.recoilXMax = 0
                    v2.recoilYMin = 0
                    v2.recoilYMax = 0
                    v2.recoilZMin = 0
                    v2.recoilZMax = 0
                else
                    v2.recoilAmount = 35
                    v2.recoilXMin = 1.25
                    v2.recoilXMax = 1.75
                    v2.recoilYMin = -1.5
                    v2.recoilYMax = 1.5
                    v2.recoilZMin = -1.5
                    v2.recoilZMax = 1.5
                end
            end
        end
    end
)

CombatSilentaimSection:Toggle({
    Title = "No Gravity",
    Default = false
    },
    function(val)
        for i,v2 in pairs(getgc(true)) do
            if typeof(v2) == "table" and rawget(v2, "recoilAmount") then
                if val then
                    v2.gravity = Vector3.new(0,0,0)
                else
                    v2.gravity = Vector3.new(0, -10, 0)
                end
            end
        end
    end
)

CombatSilentaimSection:Toggle({
    Title = "Teleport sky",
    Default = false
    },
    function(val)
        for i,v2 in pairs(getgc(true)) do
            if val then
                while task.wait(2) do
                    if Players.LocalPlayer.PlayerGui.RoactUI:FindFirstChild("BottomStatusIndicators") then
                        function TP(gotoCFrame)
							pcall(function()
								game.Players.LocalPlayer.Character.Humanoid.Sit = false
							end)
							if (game:GetService("Players")["LocalPlayer"].Character.HumanoidRootPart.Position - gotoCFrame.Position).Magnitude <= 100 then
								pcall(function() 
									tween:Cancel()
								end)
								game:GetService("Players")["LocalPlayer"].Character.HumanoidRootPart.CFrame = gotoCFrame
							else
								local tween_s = game:service"TweenService"
								local info = TweenInfo.new((game:GetService("Players")["LocalPlayer"].Character.HumanoidRootPart.Position - gotoCFrame.Position).Magnitude/78.9, Enum.EasingStyle.Linear)
								local tween, err = pcall(function()
									tween = tween_s:Create(game.Players.LocalPlayer.Character["HumanoidRootPart"], info, {CFrame = gotoCFrame})
									tween:Play()
								end)
								if not tween then return err end
							end
						end
						
						TP(CFrame.new(-193.14132690429688, -250.15985107421875, -57.2032470703125))
						wait(6)
                    else
                        wait(5)
                        function TP(gotoCFrame)
							pcall(function()
								game.Players.LocalPlayer.Character.Humanoid.Sit = false
							end)
							if (game:GetService("Players")["LocalPlayer"].Character.HumanoidRootPart.Position - gotoCFrame.Position).Magnitude <= 100 then
								pcall(function() 
									tween:Cancel()
								end)
								game:GetService("Players")["LocalPlayer"].Character.HumanoidRootPart.CFrame = gotoCFrame
							else
								local tween_s = game:service"TweenService"
								local info = TweenInfo.new((game:GetService("Players")["LocalPlayer"].Character.HumanoidRootPart.Position - gotoCFrame.Position).Magnitude/78.9, Enum.EasingStyle.Linear)
								local tween, err = pcall(function()
									tween = tween_s:Create(game.Players.LocalPlayer.Character["HumanoidRootPart"], info, {CFrame = gotoCFrame})
									tween:Play()
								end)
								if not tween then return err end
							end
						end
						
						TP(CFrame.new(-193.14132690429688, -250.15985107421875, -57.2032470703125))
						wait(6)
                    end
                end
            end
        end
    end
)

CombatSilentaimSection:Toggle({
    Title = "Auto Shoot (broken)",
    Default = false
    },
    function(val)
        for i,v in pairs(getgc(true)) do
            if typeof(v) == 'table' and rawget(v,'startShootingAfterCharge') then
                if val then
                    v.startShootingAfterCharge = true
                else
                    v.startShootingAfterCharge = false
                end
            end
        end
    end
)

miscSection:Button({
    Title = "Fling",
    ButtonName = "yeet"
    },
    function()
        local plr = game.Players.LocalPlayer
        local oldHumanoid = plr.Character.Humanoid
        local torso = game.Players.LocalPlayer.Character.HumanoidRootPart
        local flying = true
        local deb = true
        local ctrl = {f = 0, b = 0, l = 0, r = 0}
        local lastctrl = {f = 0, b = 0, l = 0, r = 0}
        local maxspeed = 50
        local speed = 50
        
        workspace.CurrentCamera.CameraSubject = torso
         
        local function Fly()
             local bambam = Instance.new("BodyThrust")
             bambam.Parent = game.Players.LocalPlayer.Character.HumanoidRootPart
             bambam.Force = Vector3.new(99999,0,99999)
             bambam.Location = game.Players.LocalPlayer.Character.HumanoidRootPart.Position
             Instance.new("SelectionBox",game.Players.LocalPlayer.Character.HumanoidRootPart).Adornee = game.Players.LocalPlayer.Character.HumanoidRootPart
             local bg = Instance.new("BodyGyro", torso)
             bg.P = 9e4
             bg.maxTorque = Vector3.new(0, 0, 0)
             bg.cframe = torso.CFrame
             local bv = Instance.new("BodyVelocity", torso)
             bv.velocity = Vector3.new(0,0,0)
             bv.maxForce = Vector3.new(9e9, 9e9, 9e9)
             repeat wait()
                 if oldHumanoid:FindFirstChildOfClass'RemoteEvent' ~= nil then
                     oldHumanoid.RagdollRemoteEvent:FireServer(true)
                 end
                 getRemote("UpdateIsCrouching"):FireServer(true)
         
                 if ctrl.l + ctrl.r ~= 0 or ctrl.f + ctrl.b ~= 0 then
                     speed = speed+.2
                     if speed > maxspeed then
                         speed = maxspeed
                     end
                 elseif not (ctrl.l + ctrl.r ~= 0 or ctrl.f + ctrl.b ~= 0) and speed ~= 0 then
                     speed = speed-1
                     if speed < 0 then
                         speed = 0
                     end
                 end
                 if (ctrl.l + ctrl.r) ~= 0 or (ctrl.f + ctrl.b) ~= 0 then
                     bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (ctrl.f+ctrl.b)) + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(ctrl.l+ctrl.r,(ctrl.f+ctrl.b)*.2,0).p) - game.Workspace.CurrentCamera.CoordinateFrame.p))*speed
                     lastctrl = {f = ctrl.f, b = ctrl.b, l = ctrl.l, r = ctrl.r}
                 elseif (ctrl.l + ctrl.r) == 0 and (ctrl.f + ctrl.b) == 0 and speed ~= 0 then
                     bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (lastctrl.f+lastctrl.b)) + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(lastctrl.l+lastctrl.r,(lastctrl.f+lastctrl.b)*.2,0).p) - game.Workspace.CurrentCamera.CoordinateFrame.p))*speed
                 else
                     bv.velocity = Vector3.new(0,0.1,0)
                 end
         
             until not flying
             ctrl = {f = 0, b = 0, l = 0, r = 0}
             lastctrl = {f = 0, b = 0, l = 0, r = 0}
             speed = 0
             bg:Destroy()
             bv:Destroy()
         
         end
         mouse.KeyDown:connect(function(key)
             if key:lower() == "w" then
                 ctrl.f = 1
             elseif key:lower() == "s" then
                 ctrl.b = -1
             elseif key:lower() == "a" then
                 ctrl.l = -1
             elseif key:lower() == "d" then
                 ctrl.r = 1
             end
         end)
         mouse.KeyUp:connect(function(key)
             if key:lower() == "w" then
                 ctrl.f = 0
             elseif key:lower() == "s" then
                 ctrl.b = 0
             elseif key:lower() == "a" then
                 ctrl.l = 0
             elseif key:lower() == "d" then
                 ctrl.r = 0
             elseif key:lower() == "r" then
         
             end
         end)
         for i,v in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
             v:Destroy()
         end -- doesnt need tools anyways
         -- hides all of ur body parts expect torso (there is a chance it doesnt work)
         wait(.1)
         oldHumanoid.RagdollRemoteEvent:FireServer(true)
         wait(.5)
         coroutine.wrap(Fly)()
         wait(.5)
         game.Players.LocalPlayer.Character.HumanoidRootPart.RootJoint.Part0 = nil
    end
)

miscSection:Textbox({
    Title = "Kill Sound (broken)",
    Default = ""
    },
    function(val)
        game:GetService("ReplicatedStorage").Shared.Assets.Sounds.KillSound.SoundId = "rbxassetid://"..val
    end

)

miscSection:Textbox({
    Title = "Hit Sound (broken)",
    Default = ""
    },
    function(val)
        game:GetService("ReplicatedStorage").Shared.Assets.Sounds.HitmarkerSound.SoundId = "rbxassetid://"..val
     end

)

ESPSection:ColorPicker({
    Title = "ESP Color",
    Default = Color3.new(255, 0, 0)
    },
    function(val)
        getgenv().TracerColor = val
        bruh.FillColor = TracerColor
    end
)

ESPSection:Toggle({
    Title = "Tracers",
    Default = false
    },
    function(val)
        tracersenabled = val
    end
)

ESPSection:Toggle({
    Title = "text",
    Default = false
    },
    function(val)
        textenabled = val
    end
)


ESPSection:Toggle({
    Title = "Boxes",
    Default = false
    },
    function(val)
        boxesenabled = val
    end
)

Players.PlayerRemoving:Connect(function(v)
    if table.find(players, v.Name) then
        table.remove(players, players[v.Name])
    end

    if boxes[v] then
        boxes[v]:Remove()
        boxes[v] = nil
    end
end)

Players.LocalPlayer.leaderstats.Score.Changed:Connect(function()
    if killsay then
        game:GetService("TextChatService").TextChannels.RBXGeneral:SendAsync(words[math.random(1, #words)])
    end
end)


for i,v in pairs(game.Players:GetPlayers()) do
    if v ~= Players.LocalPlayer then
        table.insert(players, v.Name)
        if v.Character then
            local line = Drawing.new("Line")
            line.From = Vector2.new(workspace.CurrentCamera.ViewportSize.X / 2, workspace.CurrentCamera.ViewportSize.Y)
            line.Color = TracerColor
            line.Thickness = 2
            lines[v] = line
    
            local text = Drawing.new("Text")
            text.Text = v.Name
            text.Size = 20
            text.Outline = true
            text.OutlineColor = Color3.new(0,0,0)        
            text.Center = true
            texts[v] = text

            local box = Drawing.new("Square")
            box.Thickness = 1
            box.Filled = false
            box.Visible = false
            box.ZIndex = 50
            boxes[v] = box
    
            local boxoutline = Drawing.new("Square")
            boxoutline.Visible = false
            boxoutline.Thickness = 2
            boxoutline.Filled = false
            boxoutline.ZIndex = 1
            boxoutlines[v] = boxoutline
    
            local healthbaroutline = Drawing.new("Square")
            healthbaroutline.Visible = false
            healthbaroutline.Filled = true
            healthbaroutline.Thickness = 2
            healthbaroutline.ZIndex = 1
            healthbaroutlines[v] = healthbaroutline
    
            local healthbar = Drawing.new("Square")
            healthbar.Visible = false
            healthbar.Filled = true
            healthbar.ZIndex = 50
            healthbars[v] = healthbar
        end
        v.CharacterAdded:Connect(function()
            local line = Drawing.new("Line")
            line.From = Vector2.new(workspace.CurrentCamera.ViewportSize.X / 2, workspace.CurrentCamera.ViewportSize.Y)
            line.Color = TracerColor
            line.Thickness = 2
            lines[v] = line
    
            local text = Drawing.new("Text")
            text.Text = v.Name
            text.Size = 20
            text.Outline = true
            text.OutlineColor = Color3.new(0,0,0)        
            text.Center = true
            texts[v] = text
        end)
        v.CharacterRemoving:Connect(function()
            lines[v]:Remove()
            lines[v] = nil
            texts[v]:Remove()
            texts[v] = nil
        end)
    end
end

Players.PlayerAdded:Connect(function(v)
    table.insert(players, v.Name)
    v.CharacterAdded:Connect(function()
        local line = Drawing.new("Line")
        line.From = Vector2.new(workspace.CurrentCamera.ViewportSize.X / 2, workspace.CurrentCamera.ViewportSize.Y)
        line.Color = TracerColor
        line.Thickness = 2
        lines[v] = line

        local text = Drawing.new("Text")
        text.Text = v.Name
        text.Size = 20
        text.Outline = true
        text.OutlineColor = Color3.new(0,0,0)
        text.Center = true
        texts[v] = text

        if v ~= Players.LocalPlayer and not boxes[v] then
            -- not setting the position rn because it would be kinda useless
            local box = Drawing.new("Square")
            box.Thickness = 1
            box.Filled = false
            box.Visible = false
            boxes[v] = box

            local boxoutline = Drawing.new("Square")
            boxoutline.Visible = false
            boxoutline.Thickness = 2
            boxoutline.Filled = false
            boxoutline.ZIndex = 1
            boxoutlines[v] = boxoutline
    
            local healthbaroutline = Drawing.new("Square")
            healthbaroutline.Visible = false
            healthbaroutline.Filled = true
            healthbaroutline.Thickness = 2
            healthbaroutline.ZIndex = 1
            healthbaroutlines[v] = healthbaroutline
    
            local healthbar = Drawing.new("Square")
            healthbar.Visible = false
            healthbar.Filled = true
            healthbar.ZIndex = 50
            healthbars[v] = healthbar
        end
    end)

    v.CharacterRemoving:Connect(function()
        lines[v]:Remove()
        lines[v] = nil
        texts[v]:Remove()
        texts[v] = nil
    end)
end)


mouse.KeyDown:Connect(function(v)
    if infjump and v == " " then
        Players.LocalPlayer.Character:FindFirstChildOfClass('Humanoid'):ChangeState(3)
    end
end)

UserInputService.InputBegan:Connect(function(v)
    if v.UserInputType == Enum.UserInputType.MouseButton2 then
        holdingm2 = true
        retard = getClosestToMouse()
    end
end)

UserInputService.InputEnded:Connect(function(v)
    if v.UserInputType == Enum.UserInputType.MouseButton2 then
        holdingm2 = false
        aimbotLocked = false
        UserInputService.MouseBehavior = Enum.MouseBehavior.Default
    end
end)
local bee
bee = workspace.EffectsJunk.ChildAdded:Connect(function(p)
    if Players.LocalPlayer.Character:FindFirstChildOfClass("Tool") == nil then
        shot = false
        return
    end
    local Tool = Players.LocalPlayer.Character:FindFirstChildOfClass("Tool")
    if Tool:FindFirstChild("ClientAmmo") == nil then
        shot = false
        return
    end
    if shot and p:IsA("MeshPart") then
        ARROW = p
        Instance.new("SelectionBox",p).Adornee = p
        shot = false
        print("arrow var set to arrow part")
    end
end)


for i,v in pairs(getgc(true)) do
    if typeof(v) == "table" and rawget(v,"shoot") then
        local Old = v.shoot
        v.shoot = function(tbl)
            shot = true
            arrowsshooted = tbl.shotIdx
            closest = getClosestToMouse()
            return Old(tbl)
        end
    end
    if typeof(v) == "table" and rawget(v,"calculateFireDirection") then
        old = v.calculateFireDirection
        v.calculateFireDirection = function(p3,p4,p5,p6)
            local Tool = Players.LocalPlayer.Character:FindFirstChildOfClass("Tool")
            if Tool:FindFirstChild("ClientAmmo") == nil then
                return old(p3,p4,p5,p6)
            end
            if nospread then
                p4 = 0
                p5 = 0
            end
            if silentaim and shot then
                local closesta = getClosestToMouse()
                -- funny prediction (kinda shit lmao)
                local whereHeGonnaBe = closesta.Character.Head.CFrame + (closesta.Character.Head.Velocity * 0.19 + Vector3.new(0, .1, 0))
                return (CFrame.lookAt(Tool.Contents.Handle.FirePoint.WorldCFrame.Position, whereHeGonnaBe.Position)).LookVector * 30;
            end
            return old(p3,p4,p5,p6)
        end
    end
end


task.spawn(function()
    while task.wait(KillAuraHitCooldown) do
        -- ty len for the arrow redirection
        -- really basic kill aura
        -- lags alot but works most of the time
        -- ty len
        if killaura then
            pcall(function()
                table.foreach(Players.LocalPlayer.Backpack:GetChildren(), function(i,v)
                    if v:IsA("Tool") and v:FindFirstChild("Hitboxes") then
                        weapon = v
                    end
                end)
                local c_player = getClosest()
                if c_player.Character:FindFirstChild("SemiTransparentShield").Transparency == 1 then
                    swingremote:FireServer(weapon, 1)
                    hitremote:FireServer(weapon,c_player.Character:FindFirstChild("HumanoidRootPart"),weapon.Hitboxes.Hitbox,c_player.Character:FindFirstChild("HumanoidRootPart").Position)
                    hitremote:FireServer(weapon,c_player.Character:FindFirstChild("HumanoidRootPart"),weapon.Hitboxes.Hitbox,c_player.Character:FindFirstChild("HumanoidRootPart").Position)
                end
            end)
        end

        -- silent aim pog

        if silentaim then
            pcall(function()
                local bow = Players.LocalPlayer.Character:FindFirstChildOfClass("Tool")
                if closest then
                    bruh.Adornee = getClosestToMouse().Character
                end
                if ARROW then
                    if closest then
                        if (ARROW.Position - closest.Character.HumanoidRootPart.Position).Magnitude <= 15 then
                            if silentaimhitchance == 100 then
                                firehit(closest.Character,ARROW)
                                ARROW = nil
                                shot = false
                                print("hit them (i think)")
                            else
                                local didIHitThat = calculateArrowHitChance(silentaimhitchance)
                                if didIHitThat then
                                    firehit(closest.Character,ARROW)
                                    ARROW = nil
                                    shot = false
                                    print("hit them (i think)")
                                end
                            end
                        end
                    end
                end
            end)
        end
    end
end)

-- the main loop for shit
task.spawn(function()
    RunService.Stepped:Connect(function()

        if Walkspeedenabled then
            if not UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
                Players.LocalPlayer.Character:WaitForChild("Humanoid").Walkspeed = Walkspeed
            else
                Players.LocalPlayer.Character:WaitForChild("Humanoid").Walkspeed = Walkspeed + 10.56
            end
        end
                
        if jumppowerenabled then
            Players.LocalPlayer.Character:WaitForChild("Humanoid").JumpPower = jumppower
        end
    


        if autospawn and Players.LocalPlayer.PlayerGui.RoactUI:FindFirstChild("MainMenu") then
            while wait() do
                keypress(0x20)
                keyrelease(0x20)
            end
        end
    
        -- really fuckin basic stomp aura but it works :shrug:
        if stompaura then
            for i,v in pairs(Players:GetPlayers()) do
                if v ~= Players.LocalPlayer and v.Character:FindFirstChild("Humanoid") and v.Character:FindFirstChild("Humanoid").Health <= 15 then
                    if (v.Character.HumanoidRootPart.Position - Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 4 then
                        keypress(0x51)
                        keyrelease(0x51)
                    end
                end
            end
        end
        pcall(function()
            if aimbot and holdingm2 then
                aimbotLocked = true
                if aimbotLocked and retard ~= nil then
                    -- took this from some aimbot script (prediction is hard)
                    local whereHeGonnaBe = retard.Character.HumanoidRootPart.CFrame + (retard.Character.HumanoidRootPart.Velocity * 0.19 + Vector3.new(0, .1, 0))
                    workspace.CurrentCamera.CFrame = CFrame.lookAt(workspace.CurrentCamera.CFrame.Position, whereHeGonnaBe.Position)
                    UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter
                end
            end
        end)

        if hidename then
            getRemote("UpdateIsCrouching"):FireServer(true)
        else
            getRemote("UpdateIsCrouching"):FireServer(false)
        end

        pcall(
            function()
                for i, v in pairs(Players.LocalPlayer.Backpack:GetChildren()) do
                    if v:IsA("Tool") then
                        if v:FindFirstChild("Hitboxes") ~= nil then
                            if autoequip and not Players.LocalPlayer.Character:FindFirstChildOfClass("Tool") then
                                v.Parent = Players.LocalPlayer.Character
                            end
                        end
                    end
                end
            end
        )
    
        if noclip then
            for i,v in pairs(Players.LocalPlayer.Character:GetDescendants()) do
                if v:IsA("BasePart") then
                    v.CanCollide = false
                end
            end
        end
        
        if tracersenabled then
            for player,line in pairs(lines) do
                if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    local pos,visible = workspace.CurrentCamera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
                    line.Color = TracerColor
                    line.To = Vector2.new(pos.X, pos.Y)
                    -- line.Color = player.Character.Torso.Color
                    line.Visible = visible
                else
                    line.Visible = false
                end
            end
        else
            for player,line in pairs(lines) do
                line.Visible = false
            end
        end
    
        if textenabled then
            for player,text in pairs(texts) do
                if player.Character and player.Character:FindFirstChild("Head") then
                    local head, HeadVisible = workspace.CurrentCamera:WorldToViewportPoint(player.Character.Head.Position)
                    text.Position = Vector2.new(head.X, head.Y - 28)
                    text.Color = TracerColor
                    text.Visible = HeadVisible
                else
                    text.Visible = false
                end
            end
        else
            for player,text in pairs(texts) do
                text.Visible = false
            end
        end


        -- worst esp i have written
        -- 100% could be written better but idc nigga
        if boxesenabled then
            for i,v in pairs(Players:GetPlayers()) do
                if v ~= Players.LocalPlayer and v.Character:FindFirstChild("HumanoidRootPart") and v.Character:FindFirstChild("Humanoid") and v.Character.Humanoid.Health > 0 then
                    local hrp, visible = workspace.CurrentCamera:WorldToViewportPoint(v.Character.HumanoidRootPart.Position)
                    local head, headvisible = workspace.CurrentCamera:WorldToViewportPoint(v.Character.Head.Position + Vector3.new(0, 1, 0))
                    local leg, legvisible = workspace.CurrentCamera:WorldToViewportPoint(v.Character.HumanoidRootPart.Position - Vector3.new(0,3,0))
                    local X = 2000 / hrp.Z
                    local Y = head.Y - leg.Y
                    boxes[v].Visible = visible
                    boxoutlines[v].Visible = visible
                    healthbars[v].Visible = visible
                    healthbaroutlines[v].Visible = visible
        
                    if visible then
                        boxes[v].Position = Vector2.new(hrp.X - boxes[v].Size.X / 2, hrp.Y - boxes[v].Size.Y / 2)
                        boxes[v].Size = Vector2.new(X, Y)
                        boxes[v].Color = TracerColor
                        boxoutlines[v].Position = Vector2.new(hrp.X - boxes[v].Size.X / 2, hrp.Y - boxes[v].Size.Y / 2)
                        boxoutlines[v].Size = Vector2.new(X, Y)
                        healthbaroutlines[v].Size = Vector2.new(3, Y)
                        healthbaroutlines[v].Position = Vector2.new(hrp.X - boxes[v].Size.X / 2, hrp.Y - boxes[v].Size.Y / 2) - Vector2.new(6, 0)
                        healthbars[v].Color = Color3.fromRGB(173, 0, 0):Lerp(Color3.fromRGB(0, 160, 0), v.Character:FindFirstChild("Humanoid").Health/v.Character:FindFirstChild("Humanoid").MaxHealth)
                        healthbars[v].Size = Vector2.new(2, (-healthbaroutlines[v].Size.Y - 2) * (v.Character:FindFirstChild("Humanoid").Health/v.Character:FindFirstChild("Humanoid").MaxHealth))
                        healthbars[v].Position = healthbaroutlines[v].Position + Vector2.new(1, -1 + healthbaroutlines[v].Size.Y)
                    end
                else
                    if v ~= Players.LocalPlayer then
                        boxes[v].Visible = false
                        boxoutlines[v].Visible = false
                        healthbars[v].Visible = false
                        healthbaroutlines[v].Visible = false
                    end
                end
            end
        else
            for i,v in pairs(boxes) do
                v.Visible = false
            end
            for i,v in pairs(boxoutlines) do
                v.Visible = false
            end
            for i,v in pairs(healthbars) do
                v.Visible = false
            end
            for i,v in pairs(healthbaroutlines) do
                v.Visible = false
            end
        end
       

        local Tool = Players.LocalPlayer.Character:FindFirstChildOfClass("Tool")
        if Tool ~= nil and Tool:FindFirstChild("ClientAmmo") == nil then
            return
        end

        if Players.LocalPlayer.Character then
            if Players.LocalPlayer.Character:FindFirstChild('Longbow') and instantcharge then
                for i,v in pairs(getconnections(Tool.ChargeProgressClient:GetPropertyChangedSignal("Value"))) do
                    v:Disable()
                end
                Tool.ChargeProgressClient.Value = 1
            end
        end
    end)
end)

local methodHook
methodHook = hookmetamethod(game, "__namecall", function(self, ...)
    if not checkcaller() and getnamecallmethod() == "FireServer" and antidamage and tostring(self) == "GotHitRE" then
        return
    elseif not checkcaller() and getnamecallmethod() == "FireServer" and nofall and self.Name == fallremote.Name then
        return
    elseif (getnamecallmethod() == "Kick" or getnamecallmethod() == "kick") and self == game.Players.LocalPlayer then
        return
    end
    return methodHook(self, ...)
end)

local StarterGui = game:GetService("StarterGui")

local Notifications = {
	"Credits to Gus and Superbear",
	
}

local TimeBetweenNotifications = 9000000000000

while true do
	for i = 1, #Notifications do
		local Notification = Notifications[i]
		
		StarterGui:SetCore("SendNotification", {
			Title = "frkfx",
			Text = Notification,
			Duration = 10
		})
		
		wait(TimeBetweenNotifications)
	end
end
