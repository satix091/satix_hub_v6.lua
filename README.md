-- SATIX HUB V6 — COMPLETO COM FLUTUANTE E MAIS FUNÇÕES
local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/shlexware/Orion/main/source"))()
local Library = require(game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("CommF_"))

local Window = OrionLib:MakeWindow({
    Name = "Satix Hub | Blox Fruits",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "SatixHub"
})

-- Variáveis
local AutoFarm = false
local AutoFruit = false
local AutoKillBoss = false
local BringMob = false
local ESPEnabled = false
local AutoMirage = false
local AutoKitsune = false
local AutoMission = false
local AimbotEnabled = false

-- Função auxiliar de TP
function TeleportTo(pos)
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = pos
end

-- Auto Farm
function StartAutoFarm()
    while AutoFarm do
        task.wait(1)
        local quest = GetCurrentQuest()
        if quest then
            TeleportTo(quest.QuestPos)
            wait(1)
            Library:InvokeServer("StartQuest", quest.QuestName, 1)
            for _, mob in pairs(workspace.Enemies:GetChildren()) do
                if mob.Name == quest.EnemyName and mob:FindFirstChild("Humanoid") and mob.Humanoid.Health > 0 then
                    if BringMob then
                        mob.HumanoidRootPart.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0, 5, 0)
                    else
                        TeleportTo(mob.HumanoidRootPart.CFrame * CFrame.new(0, 30, 0))
                    end
                    repeat
                        task.wait()
                        game:GetService("VirtualInputManager"):SendKeyEvent(true, "Z", false, game)
                    until mob.Humanoid.Health <= 0 or not AutoFarm
                end
            end
        end
    end
end

-- Função Auto Missão (CDK/Soul Guitar)
function StartAutoMission()
    while AutoMission do
        task.wait(10)
        -- Missão do CDK
        local quest = workspace:FindFirstChild("CDK Quest")
        if quest then
            TeleportTo(quest.Position)
            wait(1)
            Library:InvokeServer("StartQuest", "CDK Quest", 1)
        end

        -- Missão Soul Guitar
        local soulQuest = workspace:FindFirstChild("Soul Guitar Quest")
        if soulQuest then
            TeleportTo(soulQuest.Position)
            wait(1)
            Library:InvokeServer("StartQuest", "Soul Guitar Quest", 1)
        end
    end
end

-- Função Aimbot (para espada, frutas, guns, melee)
function StartAimbot()
    while AimbotEnabled do
        task.wait(0.1)
        -- Aimbot para Espada
        if game.Players.LocalPlayer.Character:FindFirstChild("Sword") then
            local closestEnemy = nil
            local distance = math.huge
            for _, enemy in pairs(workspace.Enemies:GetChildren()) do
                if enemy:FindFirstChild("Humanoid") then
                    local dist = (game.Players.LocalPlayer.Character.HumanoidRootPart.Position - enemy.HumanoidRootPart.Position).magnitude
                    if dist < distance then
                        distance = dist
                        closestEnemy = enemy
                    end
                end
            end
            if closestEnemy then
                -- Mover a espada para atacar o inimigo
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = closestEnemy.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5)
                game:GetService("VirtualInputManager"):SendKeyEvent(true, "E", false, game)
            end
        end
    end
end

-- FUNÇÕES DE TELEPORTE --------------------------------
function StartAutoMirage()
    while AutoMirage do
        task.wait(10)
        local mirage = workspace:FindFirstChild("Mirage Island")
        if mirage then
            TeleportTo(mirage.Position or mirage:GetModelCFrame().Position)
        end
    end
end

function StartAutoKitsune()
    while AutoKitsune do
        task.wait(10)
        for _, island in pairs(workspace:GetChildren()) do
            if island.Name:lower():find("kitsune") then
                TeleportTo(island:GetModelCFrame().Position)
            end
        end
    end
end

-- FUNÇÃO PARA FLUTUANTE --------------------------------
function CreateFloatingButton()
    local button = Instance.new("TextButton")
    button.Text = "Open Satix Hub"
    button.Size = UDim2.new(0, 150, 0, 50)
    button.Position = UDim2.new(0.5, -75, 0.9, -25)
    button.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.Parent = game.Players.LocalPlayer.PlayerGui

    button.MouseButton1Click:Connect(function()
        Window:Toggle()
    end)
end

-- TABS -------------------------------------------------
local FarmTab = Window:MakeTab({Name = "Auto Farm", Icon = "rbxassetid://4483345998", PremiumOnly = false})

FarmTab:AddToggle({
    Name = "Auto Farm (com Missão)",
    Default = false,
    Callback = function(v)
        AutoFarm = v
        if v then StartAutoFarm() end
    end
})

FarmTab:AddToggle({
    Name = "Bring Mob",
    Default = false,
    Callback = function(v) BringMob = v end
})

local MissionTab = Window:MakeTab({Name = "Missão", Icon = "rbxassetid://4483345998", PremiumOnly = false})

MissionTab:AddToggle({
    Name = "Auto Missão (CDK/Soul Guitar)",
    Default = false,
    Callback = function(v)
        AutoMission = v
        if v then StartAutoMission() end
    end
})

local AimbotTab = Window:MakeTab({Name = "Aimbot", Icon = "rbxassetid://4483345998", PremiumOnly = false})

AimbotTab:AddToggle({
    Name = "Aimbot Ativar",
    Default = false,
    Callback = function(v)
        AimbotEnabled = v
        if v then StartAimbot() end
    end
})

local IslandTab = Window:MakeTab({Name = "Ilhas & TP", Icon = "rbxassetid://6034299974", PremiumOnly = false})

IslandTab:AddToggle({
    Name = "Auto Mirage Island",
    Default = false,
    Callback = function(v)
        AutoMirage = v
        if v then StartAutoMirage() end
    end
})

IslandTab:AddToggle({
    Name = "Auto Kitsune Island",
    Default = false,
    Callback = function(v)
        AutoKitsune = v
        if v then StartAutoKitsune() end
    end
})

local npcList = {
    ["Blox Fruit Dealer"] = CFrame.new(2856, 57, -696),
    ["Sword Dealer"] = CFrame.new(89, 17, 1631),
    ["Quest Giver"] = CFrame.new(1060, 16, 1400),
    ["Black Market"] = CFrame.new(-463, 8, 1416),
    ["Cursed Ship Quest"] = CFrame.new(-6256, 315, -3238),
    ["Cyborg NPC"] = CFrame.new(605, 70, 262)
}

IslandTab:AddDropdown({
    Name = "Teleportar para NPC",
    Default = "",
    Options = {"Blox Fruit Dealer", "Sword Dealer", "Quest Giver", "Black Market", "Cursed Ship Quest", "Cyborg NPC"},
    Callback = function(v)
        if npcList[v] then
            TeleportTo(npcList[v])
        end
    end
})

-- Créditos
local InfoTab = Window:MakeTab({Name = "Créditos", Icon = "rbxassetid://7733765390", PremiumOnly = false})
InfoTab:AddParagraph("Satix Hub", "Desenvolvido por você + ChatGPT")
InfoTab:AddParagraph("Versão", "V6 — Funcionalidades Avançadas!")

-- Iniciar UI e Flutuante
OrionLib:Init()
CreateFloatingButton()
