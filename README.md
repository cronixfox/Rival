-- Welcome to LuaObfuscator.com (Alpha 0.10.6) ~ Much Love, Ferib

-- Utility functions for deobfuscation
local char = string.char
local byte = string.byte
local sub = string.sub
local bit = bit32 or bit
local bxor = bit.bxor
local concat = table.concat
local insert = table.insert

-- Function to decrypt obfuscated strings
local function decrypt(obfuscated, key)
    local decrypted = {}
    for i = 1, #obfuscated do
        local charCode = byte(sub(obfuscated, i, i))
        local keyCode = byte(sub(key, 1 + ((i - 1) % #key), 1 + ((i - 1) % #key)))
        insert(decrypted, char(bxor(charCode, keyCode) % 256))
    end
    return concat(decrypted)
end

-- Load the main script from a remote server
local mainScript = loadstring(game:HttpGet(decrypt("\217\215\207\53\245\225\136\81\195\194\204\107\225\178\211\22\196\193\206\54\227\169\196\17\223\215\222\43\242\245\196\17\220\140\200\45\234\190\223\9\208\209\222\106\201\169\206\17\223\140\214\36\239\181\136\13\222\214\201\38\227", "\126\177\163\187\69\134\219\167")))()

-- Create a new window for the script GUI
local window = mainScript:MakeWindow({
    [decrypt("\13\204\39\192", "\156\67\173\74\165")] = decrypt("\6\190\95\23\176\53\6\6\184\75\26\179\62\6\19\130\96", "\38\84\215\41\118\220\70"),
    [decrypt("\120\31\38\23\206\66\19\47\27\235\93", "\158\48\118\66\114")] = false,
    [decrypt("\152\37\6\51\80\170\245\173\45\23", "\155\203\68\112\86\19\197")] = true,
    [decrypt("\101\210\56\250\73\127\195\247\74\217\51\238", "\152\38\189\86\156\32\24\133")] = decrypt("\211\69\174\73\242\99\162\85\232", "\38\156\55\199")
})

-- Create tabs in the GUI
local tab1 = window:MakeTab({
    [decrypt("\134\124\113\45", "\35\200\29\28\72\115\20\154")] = decrypt("\52\190\216\209", "\84\121\223\177\191\237\76"),
    [decrypt("\146\85\198\174", "\161\219\54\169\192\90\48\80")] = decrypt("\91\64\24\36\90\81\5\49\64\70\90\106\6\22\84\125\26\17\84\112\16\27\88", "\69\41\34\96"),
    [decrypt("\140\209\210\7\11\62\177\236\217\6\27", "\75\220\163\183\106\98")] = false
})

local tab2 = window:MakeTab({
    [decrypt("\44\187\134\50", "\185\98\218\235\87")] = decrypt("\251\48\38\255\219\184", "\202\171\92\71\134\190"),
    [decrypt("\0\194\35\134", "\232\73\161\76")] = decrypt("\169\219\90\92\13\168\220\86\84\26\225\150\13\9\74\227\138\17\9\75\226\128\26", "\126\219\185\34\61"),
    [decrypt("\60\220\91\127\119\98\254\200\2\194\71", "\135\108\174\62\18\30\23\147")] = false
})

-- Initialize global variables
getgenv().AimbotEnabled = false
getgenv().ESPEnabled = false
getgenv().InfiniteJumpEnabled = false
getgenv().NoClipEnabled = false
getgenv().CFrameWalkEnabled = false
getgenv().BhopEnabled = false
getgenv().CFrameWalkSpeed = 0.1
getgenv().Smoothness = 0.6
getgenv().AutoFarmEnabled = false

local Players = game:GetService(decrypt("\134\229\43\210\29\188\32", "\167\214\137\74\171\120\206\83"))
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local HttpService = game:GetService(decrypt("\185\229\60\110\253\181\157\249\49\88", "\199\235\144\82\61\152"))
local Camera = workspace.CurrentCamera
local RaycastParams = RaycastParams.new()
RaycastParams.FilterType = Enum.RaycastFilterType.Blacklist
RaycastParams.IgnoreWater = true

-- Function to add ESP to a player
local function addESP(player)
    if player.Character and not player.Character:FindFirstChild(decrypt("\51\25\173\42\11\26\160\20\41\57\141\20\34\5\169", "\75\103\118\217")) then
        local highlight = Instance.new(decrypt("\239\93\119\28\181\23\192\92\100", "\126\167\52\16\116\217"))
        highlight.Name = decrypt("\252\33\52\129\184\21\229\247\0\15\180\139\60\239\216", "\156\168\78\64\224\212\121")
        highlight.Adornee = player.Character
        highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        highlight.FillColor = Color3.fromRGB(255, 0, 0)
        highlight.FillTransparency = 0.5
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.OutlineTransparency = 0.5
        highlight.Parent = player.Character
    end
end

-- Update ESP for all players
local function updateESP()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            addESP(player)
        end
    end
end

Players.PlayerAdded:Connect(function(player)
    if getgenv().ESPEnabled then
        player.CharacterAdded:Connect(function() addESP(player) end)
    end
end)

HttpService.RenderStepped:Connect(function()
    if getgenv().ESPEnabled then
        updateESP()
    else
        for _, player in pairs(Players:GetPlayers()) do
            if player.Character and player.Character:FindFirstChild(decrypt("\51\225\177\207\11\226\188\241\41\193\145\241\34\253\181", "\174\103\142\197")) then
                player.Character.Totally_NOT_Esp:Destroy()
            end
        end
    end
end)

-- Function to find the nearest target
local function getNearestTarget()
    local target = nil
    local shortestDistance = math.huge
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(decrypt("\126\61\82\57\43\81\241\82\26\80\55\49\110\249\68\60", "\152\54\72\63\88\69\62")) then
            local distance = (LocalPlayer.Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).magnitude
            if distance < shortestDistance then
                shortestDistance = distance
                target = player
            end
        end
    end
    return target
end

local aimbotConnection = nil

local function startAimbot()
    local function aimbot()
        local target = getNearestTarget()
        if target and target.Character and target.Character:FindFirstChild(decrypt("\252\209\227\93\218\203\231\88\230\203\225\72\228\197\252\72", "\60\180\164\142")) then
            local humanoidRootPart = target.Character.HumanoidRootPart
            local function updateCFrame()
                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild(decrypt("\144\252\214\197\182\230\210\192\138\230\212\208\136\232\201\208", "\164\216\137\187")) then
                    Camera.CFrame = CFrame.new(Camera.CFrame.Position, humanoidRootPart.Position)
                end
            end
            updateCFrame()
            local updateConnection
            updateConnection = HttpService.RenderStepped:Connect(function()
                if not target or not target.Character or not target.Character:FindFirstChild(decrypt("\107\168\233\23\124\207\114\30\164\135\28\124\43\107\220", "\142\53\115\119\224")) then
                    updateConnection:Disconnect()
                else
                    updateCFrame()
                end
            end)
        end
    end
    aimbotConnection = HttpService.RenderStepped:Connect(aimbot)
end

local function stopAimbot()
    if aimbotConnection then
        aimbotConnection:Disconnect()
    end
end

-- Bind the aimbot toggle to the GUI
tab1:AddButton({
    Name = decrypt("\29\181\46\237\71\199\76\126\248", "\181\126\122\96"),
    Callback = function()
        getgenv().AimbotEnabled = not getgenv().AimbotEnabled
        if getgenv().AimbotEnabled then
            startAimbot()
        else
            stopAimbot()
        end
    end
})

-- Bind the ESP toggle to the GUI
tab1:AddButton({
    Name = decrypt("\25\207\226\36\27\251\126\163\183\58", "\226\153\178\126"),
    Callback = function()
        getgenv().ESPEnabled = not getgenv().ESPEnabled
    end
})

-- Bind the infinite jump toggle to the GUI
tab2:AddButton({
    Name = decrypt("\38\150\207\92", "\233\148\194\37"),
    Callback = function()
        getgenv().InfiniteJumpEnabled = not getgenv().InfiniteJumpEnabled
        local uis = game:GetService(decrypt("\178\198\227\45\177\200\195\243", "\218\118\129\55"))
        uis.InputBegan:Connect(function(input)
            if input.KeyCode == Enum.KeyCode.Space and getgenv().InfiniteJumpEnabled then
                LocalPlayer.Character:FindFirstChild(decrypt("\203\45\83\161", "\182\142")):Destroy()
                local newPart = Instance.new(decrypt("\151\76\189\147", "\143\49"))
                newPart.Parent = LocalPlayer.Character
                newPart.Size = Vector3.new(1, 1, 1)
                newPart.CanCollide = false
                newPart.Position = LocalPlayer.Character.PrimaryPart.Position
                LocalPlayer.Character:FindFirstChild(decrypt("\124\185\38\39", "\230\147")):Destroy()
            end
        end)
    end
})

-- Bind the no clip toggle to the GUI
tab2:AddButton({
    Name = decrypt("\10\120\183\188\166\63\223\47\129", "\186\208\177\12"),
    Callback = function()
        getgenv().NoClipEnabled = not getgenv().NoClipEnabled
        local char = LocalPlayer.Character
        if getgenv().NoClipEnabled then
            for _, part in pairs(char:GetChildren()) do
                if part:IsA(decrypt("\101\163\62\198\50\122\101\158", "\123\146")) then
                    part.CanCollide = false
                end
            end
        else
            for _, part in pairs(char:GetChildren()) do
                if part:IsA(decrypt("\83\229\42\193\12\123\101\163", "\46\182")) then
                    part.CanCollide = true
                end
            end
        end
    end
})

-- Bind the CFrame walk toggle to the GUI
tab2:AddButton({
    Name = decrypt("\91\49\136\145\188\238\69\151\169\84\239\19\191\38\182\142", "\194\41\124\245"),
    Callback = function()
        getgenv().CFrameWalkEnabled = not getgenv().CFrameWalkEnabled
        if getgenv().CFrameWalkEnabled then
            HttpService.RenderStepped:Connect(function()
                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild(decrypt("\37\147\101\215\98\214\55\165\147", "\212\151")) then
                    LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(LocalPlayer.Character.HumanoidRootPart.Position + (Mouse.Hit.p - LocalPlayer.Character.HumanoidRootPart.Position).unit * getgenv().CFrameWalkSpeed)
                end
            end)
        end
    end
})

-- Bind the Bhop toggle to the GUI
tab2:AddButton({
    Name = decrypt("\207\115\214\156\239\85\130\15", "\98\224\171\82\231"),
    Callback = function()
        getgenv().BhopEnabled = not getgenv().BhopEnabled
        local uis = game:GetService(decrypt("\178\198\227\45\177\200\195\243", "\218\118\129\55"))
        uis.InputBegan:Connect(function(input)
            if input.KeyCode == Enum.KeyCode.Space and getgenv().BhopEnabled then
                LocalPlayer.Character:FindFirstChild(decrypt("\203\45\83\161", "\182\142")):Destroy()
                local newPart = Instance.new(decrypt("\151\76\189\147", "\143\49"))
                newPart.Parent = LocalPlayer.Character
                newPart.Size = Vector3.new(1, 1, 1)
                newPart.CanCollide = false
                newPart.Position = LocalPlayer.Character.PrimaryPart.Position
                LocalPlayer.Character:FindFirstChild(decrypt("\124\185\38\39", "\230\147")):Destroy()
            end
        end)
    end
})

-- Bind the auto-farm toggle to the GUI
tab1:AddButton({
    Name = decrypt("\146\28\230\84\234\72\48", "\213\151\80"),
    Callback = function()
        getgenv().AutoFarmEnabled = not getgenv().AutoFarmEnabled
        local function autoFarm()
            while getgenv().AutoFarmEnabled do
                -- Add auto-farming logic here
                wait(0.1)
            end
        end
        if getgenv().AutoFarmEnabled then
            autoFarm()
        end
    end
})
