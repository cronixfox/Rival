-- Import the Orion Library
local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/shlexware/Orion/main/source"))()
local Window = OrionLib:MakeWindow({Name = "Advanced Script", HidePremium = true, SaveConfig = true, ConfigFolder = "OrionTest"})

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

-- Create tabs in the GUI
local tab1 = Window:NewTab("Main")
local tab2 = Window:NewTab("Features")

-- Create sections in the tabs
local mainSection = tab1:NewSection("Main Functions")
local featuresSection = tab2:NewSection("Additional Features")

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
mainSection:NewButton("Toggle Aimbot", "Enable or disable the aimbot", function()
    getgenv().AimbotEnabled = not getgenv().AimbotEnabled
    if getgenv().AimbotEnabled then
        startAimbot()
    else
        stopAimbot()
    end
end)

-- Bind the ESP toggle to the GUI
mainSection:NewButton("Toggle ESP", "Enable or disable ESP", function()
    getgenv().ESPEnabled = not getgenv().ESPEnabled
end)

-- Bind the infinite jump toggle to the GUI
featuresSection:NewButton("Toggle Infinite Jump", "Enable or disable infinite jump", function()
    getgenv().InfiniteJumpEnabled = not getgenv().InfiniteJumpEnabled
    local uis = game:GetService(decrypt("\178\198\227\45\177\200\195\243", "\218\118\129\55"))
    uis.InputBegan:Connect(function(input)
        if input.KeyCode == Enum.KeyCode.Space and getgenv().InfiniteJumpEnabled then
            LocalPlayer.Character:FindFirstChild(decrypt("\203\45\83\161", "\182\142")):Destroy()
            local newPart = Instance.new(decrypt("\151\76\189\147", "\143\49"))
            newPart.Parent = LocalPlayer.Character
            newPart.Size = Vector3.new(1, 1, 1)
            newPart.Anchored = true
            newPart.CanCollide = false
            newPart.CFrame = LocalPlayer.Character.HumanoidRootPart.CFrame
            newPart.Touched:Connect(function()
                newPart:Destroy()
            end)
        end
    end)
end)

-- Bind the no-clip toggle to the GUI
featuresSection:NewButton("Toggle No-Clip", "Enable or disable no-clip", function()
    getgenv().NoClipEnabled = not getgenv().NoClipEnabled
    local function onCharacterAdded(character)
        if getgenv().NoClipEnabled then
            for _, part in pairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
        end
    end
    LocalPlayer.CharacterAdded:Connect(onCharacterAdded)
    if getgenv().NoClipEnabled then
        onCharacterAdded(LocalPlayer.Character)
    end
end)

-- Bind the CFrame Walk toggle to the GUI
featuresSection:NewButton("Toggle CFrame Walk", "Enable or disable CFrame walk", function()
    getgenv().CFrameWalkEnabled = not getgenv().CFrameWalkEnabled
    local function onUpdate()
        if getgenv().CFrameWalkEnabled then
            LocalPlayer.Character.HumanoidRootPart.CFrame = Camera.CFrame * CFrame.new(0, 0, getgenv().CFrameWalkSpeed)
        end
    end
    HttpService.RenderStepped:Connect(onUpdate)
end)

-- Bind the Bhop toggle to the GUI
featuresSection:NewButton("Toggle Bhop", "Enable or disable Bhop", function()
    getgenv().BhopEnabled = not getgenv().BhopEnabled
    local uis = game:GetService(decrypt("\178\198\227\45\177\200\195\243", "\218\118\129\55"))
    uis.InputBegan:Connect(function(input)
        if input.KeyCode == Enum.KeyCode.Space and getgenv().BhopEnabled then
            LocalPlayer.Character:FindFirstChild(decrypt("\203\45\83\161", "\182\142")):Destroy()
        end
    end)
end)

-- Bind the Auto-Farm toggle to the GUI
featuresSection:NewButton("Toggle Auto-Farm", "Enable or disable Auto-Farm", function()
    getgenv().AutoFarmEnabled = not getgenv().AutoFarmEnabled
end)

-- Initialize the GUI
OrionLib:Init()
