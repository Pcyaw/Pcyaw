-- Variables
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local replicatedStorage = game:GetService("ReplicatedStorage")
local teleportService = game:GetService("TeleportService")
local placeId = game.PlaceId

-- Function to auto farm
local function autoFarm()
    while true do
        for i, enemy in pairs(game.Workspace.Enemies:GetChildren()) do
            if enemy:FindFirstChild("HumanoidRootPart") and enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                humanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame
                wait(0.5)  -- Adjust timing as needed
                replicatedStorage.Remotes.CommF_:InvokeServer("Attack", enemy)
            end
        end
        wait(1)  -- Delay before repeating the loop
    end
end

-- Function to find and defeat elite bosses
local function autoElite()
    while true do
        for _, boss in pairs(game.Workspace.Bosses:GetChildren()) do
            if boss:FindFirstChild("HumanoidRootPart") and boss:FindFirstChild("Humanoid") and boss.Humanoid.Health > 0 then
                humanoidRootPart.CFrame = boss.HumanoidRootPart.CFrame
                wait(0.5)  -- Adjust timing as needed
                replicatedStorage.Remotes.CommF_:InvokeServer("Attack", boss)
            end
        end
        wait(1)  -- Delay before repeating the loop
    end
end

-- Function to hop servers
local function serverHop()
    local servers = teleportService:GetTeleportData()
    for _, server in pairs(servers) do
        if server.PlaceId == placeId and server.Id ~= game.JobId then
            teleportService:TeleportToPlaceInstance(placeId, server.Id, player)
            break
        end
    end
end

-- Start auto farm, auto elite, and server hop
spawn(autoFarm)
spawn(autoElite)

-- Periodically hop servers
while true do
    wait(600)  -- 10 minutes delay before hopping to the next server
    serverHop()
end
