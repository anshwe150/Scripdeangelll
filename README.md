-- Servicio de jugadores y ReplicatedStorage
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- Crear una interfaz de usuario
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 150, 0, 50)
frame.Position = UDim2.new(0.5, -75, 0.5, -25)
frame.BackgroundColor3 = Color3.new(0, 0, 0)
frame.Parent = screenGui

local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0.5, 0, 1, 0)
toggleButton.Position = UDim2.new(0, 0, 0, 0)
toggleButton.Text = "Bate On"
toggleButton.BackgroundColor3 = Color3.new(1, 1, 1)
toggleButton.TextColor3 = Color3.new(0, 0, 0)
toggleButton.Parent = frame

local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0.5, 0, 1, 0)
closeButton.Position = UDim2.new(0.5, 0, 0, 0)
closeButton.Text = "X"
closeButton.BackgroundColor3 = Color3.new(1, 0, 0)
closeButton.TextColor3 = Color3.new(1, 1, 1)
closeButton.Parent = frame

-- Variable para controlar el estado del bate
local isBateActive = false

-- Función para activar/desactivar el bate
local function toggleBate()
    isBateActive = not isBateActive
    if isBateActive then
        toggleButton.Text = "Bate Off"
    else
        toggleButton.Text = "Bate On"
    end
end

-- Conectar el botón de toggle a la función de toggle
toggleButton.MouseButton1Click:Connect(toggleBate)

-- Función para encontrar al jugador más cercano
local function findClosestPlayer()
    local closestPlayer = nil
    local closestDistance = math.huge

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") then
            local distance = (game.Players.LocalPlayer.Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).magnitude
            if distance < closestDistance then
                closestPlayer = player
                closestDistance = distance
            end
        end
    end

    return closestPlayer, closestDistance
end

-- Función para atacar con el bate
local function attackWithBate()
    if isBateActive then
        local closestPlayer, distance = findClosestPlayer()
        if closestPlayer and distance < 20 then -- Ajusta el rango según sea necesario
            closestPlayer.Character.Humanoid:TakeDamage(20) -- Ajusta el daño según sea necesario
            print("Atacaste a", closestPlayer.Name, "con el bate!")
        end
    end
end

-- Conectar la función de ataque a un evento de entrada, por ejemplo, el clic del ratón
game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.UserInputType == Enum.UserInputType.MouseButton1 then
        attackWithBate()
    end
end)

-- Función para agrandar el hitbox del jugador
local function enlargeHitbox(player)
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        player.Character.Humanoid.HipHeight = 10 -- Ajusta el valor según sea necesario
        player.Character.Humanoid.WalkSpeed = 0 -- Para que el jugador no se mueva
    end
end

-- Conectar la función de engrandecer hitbox a un evento, por ejemplo, al activar el bate
toggleButton.MouseButton1Click:Connect(function()
    local closestPlayer, distance = findClosestPlayer()
    if closestPlayer and distance < 20 then -- Ajusta el rango según sea necesario
        enlargeHitbox(closestPlayer)
    end
end)

-- Función para ocultar/mostrar la interfaz
local function toggleInterfaceVisibility()
    screenGui.Enabled = not screenGui.Enabled
end

-- Conectar el botón de cierre a la función de toggle de visibilidad
closeButton.MouseButton1Click:Connect(toggleInterfaceVisibility)
