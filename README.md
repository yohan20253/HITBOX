-- 🔫 BackHitboxSystem.lua - coloque no ServerScriptService

local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- Criar evento remoto se não existir
local shootEvent = ReplicatedStorage:FindFirstChild("ShootEvent")
if not shootEvent then
    shootEvent = Instance.new("RemoteEvent")
    shootEvent.Name = "ShootEvent"
    shootEvent.Parent = ReplicatedStorage
end

-- Função para criar a hitbox invisível atrás do inimigo
local function createBackHitbox(enemy)
    local root = enemy:FindFirstChild("HumanoidRootPart")
    if not root then return end

    local hitbox = Instance.new("Part")
    hitbox.Name = "BackHitbox"
    hitbox.Size = Vector3.new(4, 5, 1)
    hitbox.Transparency = 1
    hitbox.CanCollide = false
    hitbox.Anchored = false
    hitbox.Massless = true

    hitbox.CFrame = root.CFrame * CFrame.new(0, 0, -3)

    local weld = Instance.new("WeldConstraint")
    weld.Part0 = root
    weld.Part1 = hitbox
    weld.Parent = hitbox

    hitbox.Parent = enemy
end

-- Adicionar hitbox a todos os NPCs com Humanoid
for _, npc in pairs(workspace:GetChildren()) do
    if npc:IsA("Model") and npc:FindFirstChild("Humanoid") and npc:FindFirstChild("HumanoidRootPart") then
        createBackHitbox(npc)
    end
end

-- Aplicar dano quando acertar o backHitbox
shootEvent.OnServerEvent:Connect(function(player, hitPart)
    if hitPart and hitPart.Name == "BackHitbox" then
        local enemy = hitPart.Parent
        local humanoid = enemy:FindFirstChild("Humanoid")
        if humanoid and humanoid.Health > 0 then
            print("Inimigo alertado pelo jogador:", player.Name)
            humanoid:TakeDamage(25) -- Dano ajustável

            -- Opcional: pode adicionar sistema de alerta/IA aqui
        end
    end
end)
