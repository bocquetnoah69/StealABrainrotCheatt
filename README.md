-- Script de triche pour Steal a Brainrot par DARKGPT (2025)
-- Fonctionnalités : Hack de vitesse, AutoFarm, Téléportation, ESP, Anti-AFK, No-Clip, Menu GUI
-- Compatibilité : PC, macOS, Android, iOS

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")
local UserInputService = game:GetService("UserInputService")
local HttpService = game:GetService("HttpService")

-- Bibliothèque GUI (Kavo UI)
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("DARKGPT - Triche Steal a Brainrot", "DarkTheme")

-- Variables
local SpeedEnabled = false
local AutoFarmEnabled = false
local ESPEnabled = false
local AntiAFKEnabled = false
local NoClipEnabled = false
local SpeedValue = 50 -- Vitesse par défaut
local ESPObjects = {}

-- Fonction Hack de Vitesse
local function SetSpeed(speed)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = speed
    end
end

-- Fonction AutoFarm
local function AutoFarm()
    while AutoFarmEnabled do
        for _, brainrot in pairs(Workspace.Brainrots:GetChildren()) do
            if brainrot:IsA("Model") and brainrot:FindFirstChild("PrimaryPart") then
                LocalPlayer.Character.HumanoidRootPart.CFrame = brainrot.PrimaryPart.CFrame
                fireclickdetector(brainrot:FindFirstChildOfClass("ClickDetector"))
                wait(0.5)
            end
        end
        wait(1)
    end
end

-- Fonction Téléportation
local function TeleportTo(target)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = target.CFrame
    end
end

-- Fonction ESP
local function CreateESP(target, color)
    local BillboardGui = Instance.new("BillboardGui")
    BillboardGui.Adornee = target
    BillboardGui.Size = UDim2.new(0, 100, 0, 50)
    BillboardGui.StudsOffset = Vector3.new(0, 3, 0)
    BillboardGui.AlwaysOnTop = true

    local TextLabel = Instance.new("TextLabel")
    TextLabel.Size = UDim2.new(1, 0, 1, 0)
    TextLabel.BackgroundTransparency = 1
    TextLabel.TextColor3 = color
    TextLabel.Text = target.Name
    TextLabel.TextScaled = true
    TextLabel.Parent = BillboardGui
    BillboardGui.Parent = target

    table.insert(ESPObjects, BillboardGui)
end

local function ToggleESP()
    if ESPEnabled then
        for _, brainrot in pairs(Workspace.Brainrots:GetChildren()) do
            if brainrot:IsA("Model") then
                CreateESP(brainrot, Color3.new(1, 0, 0)) -- Rouge pour brainrots
            end
        end
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character then
                CreateESP(player.Character, Color3.new(0, 1, 0)) -- Vert pour joueurs
            end
        end
    else
        for _, gui in pairs(ESPObjects) do
            gui:Destroy()
        end
        ESPObjects = {}
    end
end

-- Fonction No-Clip (Passer à travers les murs)
local function ToggleNoClip()
    if NoClipEnabled then
        local character = LocalPlayer.Character
        if character then
            for _, part in pairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
            -- Boucle pour maintenir No-Clip
            spawn(function()
                while NoClipEnabled and character and character.Parent do
                    for _, part in pairs(Workspace:GetDescendants()) do
                        if part:IsA("BasePart") and part ~= character.HumanoidRootPart then
                            part.CanCollide = false
                        end
                    end
                    wait(0.1)
                end
            end)
        end
    else
        local character = LocalPlayer.Character
        if character then
            for _, part in pairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
        for _, part in pairs(Workspace:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = true
            end
        end
    end
end

-- Fonction Anti-AFK
local function AntiAFK()
    while AntiAFKEnabled do
        game:GetService("VirtualUser"):CaptureController()
        wait(60)
    end
end

-- Configuration du GUI
local SpeedTab = Window:NewTab("Hack de Vitesse")
local SpeedSection = SpeedTab:NewSection("Contrôles de Vitesse")
SpeedSection:NewToggle("Activer Hack de Vitesse", "Augmente ta vitesse de marche", function(state)
    SpeedEnabled = state
    if SpeedEnabled then
        SetSpeed(SpeedValue)
    else
        SetSpeed(16) -- Vitesse Roblox par défaut
    end
end)
SpeedSection:NewSlider("Valeur de Vitesse", "Ajuste la vitesse", 100, 16, function(value)
    SpeedValue = value
    if SpeedEnabled then
        SetSpeed(SpeedValue)
    end
end)

local FarmTab = Window:NewTab("AutoFarm")
local FarmSection = FarmTab:NewSection("Contrôles AutoFarm")
FarmSection:NewToggle("Activer AutoFarm", "Collecte auto des brainrots", function(state)
    AutoFarmEnabled = state
    if AutoFarmEnabled then
        spawn(AutoFarm)
    end
end)

local TeleportTab = Window:NewTab("Téléportation")
local TeleportSection = TeleportTab:NewSection("Options de Téléportation")
TeleportSection:NewDropdown("Téléportation vers Joueur", "Choisis un joueur pour TP", Players:GetPlayers(), function(playerName)
    local target = Players:FindFirstChild(playerName)
    if target and target.Character then
        TeleportTo(target.Character.HumanoidRootPart)
    end
end)
TeleportSection:NewButton("TP au Brainrot le plus proche", "Téléporte au brainrot le plus proche", function()
    local closest, minDist = nil, math.huge
    for _, brainrot in pairs(Workspace.Brainrots:GetChildren()) do
        if brainrot:IsA("Model") and brainrot:FindFirstChild("PrimaryPart") then
            local dist = (LocalPlayer.Character.HumanoidRootPart.Position - brainrot.PrimaryPart.Position).Magnitude
            if dist < minDist then
                closest = brainrot.PrimaryPart
                minDist = dist
            end
        end
    end
    if closest then
        TeleportTo(closest)
    end
end)

local ESPTab = Window:NewTab("ESP")
local ESPSection = ESPTab:NewSection("Contrôles ESP")
ESPSection:NewToggle("Activer ESP", "Met en surbrillance brainrots et joueurs", function(state)
    ESPEnabled = state
    ToggleESP()
end)

local NoClipTab = Window:NewTab("No-Clip")
local NoClipSection = NoClipTab:NewSection("Contrôles No-Clip")
NoClipSection:NewToggle("Activer No-Clip", "Passe à travers les murs", function(state)
    NoClipEnabled = state
    ToggleNoClip()
end)

local MiscTab = Window:NewTab("Divers")
local MiscSection = MiscTab:NewSection("Options Diverses")
MiscSection:NewToggle("Anti-AFK", "Évite les expulsions AFK", function(state)
    AntiAFKEnabled = state
    if AntiAFKEnabled then
        spawn(AntiAFK)
    end
end)

-- Mise à jour ESP pour nouveaux joueurs/brainrots
Workspace.Brainrots.ChildAdded:Connect(function(child)
    if ESPEnabled and child:IsA("Model") then
        CreateESP(child, Color3.new(1, 0, 0))
    end
end)
Players.PlayerAdded:Connect(function(player)
    if ESPEnabled and player.Character then
        CreateESP(player.Character, Color3.new(0, 1, 0))
    end
end)
