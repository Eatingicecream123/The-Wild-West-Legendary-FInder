# d

local script = [[



    local Settings = {
        Hop_Delay = 3, 
        Legendary_Animal = true,
        Thunderstruck_Tree = true
    }
    
    repeat wait() until game:GetService("Players") and game:GetService("Players").LocalPlayer;
    
    
   -- 1
   
   
    local queue_on_tp = queue_on_teleport or syn and syn.queue_on_teleport or false
    local rconsolename = rconsolename or rconsolesettitle or false

    if not queue_on_tp or not rconsolename then warn("Not Compatible Exploit") return end
    
    -- 2
    
    if rconsolecreate then rconsolecreate() end
    rconsoleclear();
    rconsolename("Newest Method of Grinding ;)")
    
    -- Color Printing to Console
    local function setColor(color)
        if syn then
            rconsoleprint(color)
        end
    end
    
    setColor("@@WHITE@@")
    rconsoleprint("Restarting:\n", "white")
    
    if game:GetService("ReplicatedStorage"):FindFirstChild("Beep") then
        game:GetService("ReplicatedStorage"):FindFirstChild("Beep"):Destroy()
    end
    
    local Beep_Sound = Instance.new("Sound")
    Beep_Sound.Parent = game:GetService("ReplicatedStorage")
    Beep_Sound.Name = "Beep"
    Beep_Sound.SoundId = "rbxassetid://993959716"
    Beep_Sound.Volume = 3
    
    ---------------------------------
    --      Find Legendaries
    ---------------------------------
    local function findInterests()
        setColor("@@LIGHT_CYAN@@")
        rconsoleprint("Waiting for game...\n", "cyan")
    
        repeat wait() until game:GetService("Workspace") and game:GetService("Workspace"):FindFirstChild("WORKSPACE_Entities") and game:GetService("Workspace")["WORKSPACE_Entities"]:FindFirstChild("Animals") and game:GetService("Workspace")["WORKSPACE_Entities"].Animals:FindFirstChildOfClass("Model") and game:GetService("Workspace"):FindFirstChild("WORKSPACE_Geometry"); wait()
    
        local result = {}
    
        if Settings.Legendary_Animal then
            for i,v in pairs(game:GetService("Workspace")["WORKSPACE_Entities"].Animals:GetChildren()) do
                local health = v:WaitForChild("Health")
                if health and health.Value > 300 then 
                    table.insert(result, {"Animal", v})
                end
            end
        end
    
        if Settings.Thunderstruck_Tree then
            for i,v in pairs(game:GetService("Workspace")["WORKSPACE_Geometry"]:GetDescendants()) do
                if v:IsA("ParticleEmitter") and v.Name == "Strike2" then
                    table.insert(result, {"Legendary", v.Parent.Parent})
                end
            end
        end
    
        if #result == 0 then
            return nil
        end
        return result
    end
    
    -- Get/Process Result
    local result = findInterests()
    if result then
        -- Services
        local Camera = workspace.Camera
        local Player = game:GetService("Players").LocalPlayer
        local RS = game:GetService("RunService")
        local DRAW = Drawing.new
        local RGB = Color3.fromRGB
        local V2 = Vector2.new
        local ROUND = math.round
        local toPoint = Camera.WorldToViewportPoint
    
        ------------------------------------
        --          ESP Library
        ------------------------------------
        local ESP = {}
        function ESP:add(object, name, col, type) 
            local NAME = DRAW("Text")
            NAME.Text = name
            NAME.Size = 16
            NAME.Color = col
            NAME.Center = true
            NAME.Visible = true
            NAME.Transparency = 1
            NAME.Position = V2(0, 0)
            NAME.Outline = true
            NAME.OutlineColor = RGB(10, 10, 10)
            NAME.Font = 3
            
            local INFO = DRAW("Text")
            INFO.Text = "[]"
            INFO.Size = 14
            INFO.Color = RGB(255, 255, 255)
            INFO.Center = true
            INFO.Visible = true
            INFO.Transparency = 1
            INFO.Position = V2(0, 0)
            INFO.Outline = true
            INFO.OutlineColor = RGB(10, 10, 10)
            INFO.Font = 3

            local startHealth = 0
            if type == "Animal" and object:FindFirstChild("Health") then
                startHealth = object.Health.Value
            end
            
            local function Update()
                local c
                c = RS.RenderStepped:Connect(function()
                    if not (object.Parent and object.Parent.Parent and object.PrimaryPart) then
                        NAME.Visible = false
                        INFO.Visible = false
                        if not object.Parent or not object.Parent.Parent then
                            NAME:Remove()
                            INFO:Remove()
                            c:Disconnect()
                        end

                        return
                    end

                    local pos, vis = toPoint(Camera, object.PrimaryPart.Position)
                    if vis then
                        NAME.Position = V2(pos.X, pos.Y)
                        
                        if Player.Character and Player.Character.PrimaryPart then
                            INFO.Position = NAME.Position + V2(0, NAME.TextBounds.Y/1.2)
                            local distText = "["..ROUND((Player.Character.PrimaryPart.Position - object.PrimaryPart.Position).magnitude).."m]"
                            
                            local healthText = ""
                            if type == "Animal" and object:FindFirstChild("Health") then
                                healthText = "["..ROUND(object.Health.Value).."/"..startHealth.."]"
                            end

                            INFO.Text = distText .. healthText
                            INFO.Visible = true
                        else
                            INFO.Visible = false
                        end
                        
                        NAME.Visible = true
                    else
                        NAME.Visible = false
                        INFO.Visible = false
                    end
                end)
            end
            coroutine.wrap(Update)()
        end
    
        ----------------------------------
        --          Draw ESP
        ----------------------------------
        Beep_Sound:Play()

        setColor("@@LIGHT_RED@@")
        for _,v in next, result do
            if v[1] == "Animal" then
                rconsoleprint("Found Legendary "..v[2].Name.." !\n", "red")
                ESP:add(v[2], "Legendary "..v[2].Name, RGB(255, 248, 145), "Animal")
            elseif v[1] == "Tree" then
                rconsoleprint("Found Thunderstruck Tree !\n", "red")
                ESP:add(v[2], "Thunderstruck Tree", RGB(0, 255, 208), "Tree")
            end
        end
    else
        -- Services
        local HTTPS = game:GetService("HttpService")
        local TPS = game:GetService("TeleportService")
    
        setColor("@@LIGHT_RED@@")
        rconsoleprint("Found 0\n", "red")
        
        -- Get Random
        local Servers = HTTPS:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100"))
        local new = math.random(1, #Servers.data)
        if Servers.data[new].playing >= Servers.data[new].maxPlayers-1 then
            repeat 
                new = math.random(1, #Servers.data)
                wait()
            until Servers.data[new].playing < Servers.data[new].maxPlayers-1
        end

        wait(Settings.Hop_Delay)
        
        setColor("@@WHITE@@")
        rconsoleprint("Teleporting to Server: ", "white")
    
        setColor("@@LIGHT_CYAN@@")
        rconsoleprint(new.."\n", "cyan")

        queue_on_tp('loadstring(readfile("TWW_Hopper.lua"))()')
        TPS:TeleportToPlaceInstance(game.PlaceId, Servers.data[new].id)
    end
]]

writefile("TWW_Hopper.lua", script)
wait(0.5)
loadstring(readfile("TWW_Hopper.lua"))()
