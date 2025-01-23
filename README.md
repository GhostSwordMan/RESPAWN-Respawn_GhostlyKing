local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- Variable global para el estado del script
if not _G.RespawnScript then
    _G.RespawnScript = { Active = false, Connections = {} }
end

-- FunciÃ³n para desconectar todas las conexiones activas
local function disconnectAll()
    for _, connection in ipairs(_G.RespawnScript.Connections) do
        if connection and connection.Connected then
            connection:Disconnect()
        end
    end
    _G.RespawnScript.Connections = {}
end

-- FunciÃ³n para manejar el respawn
local function onCharacterAdded(character)
    local humanoid = character:WaitForChild("Humanoid")
    local deathConnection = humanoid.Died:Connect(function()
        if _G.RespawnScript.Active and ReplicatedStorage:FindFirstChild("Guide") then
            ReplicatedStorage.Guide:FireServer()
        end
    end)

    -- Almacena la conexiÃ³n para desconectarla al desactivar
    table.insert(_G.RespawnScript.Connections, deathConnection)
end

-- ConfiguraciÃ³n del script
if _G.RespawnScript.Active then
    -- Desactiva el script y limpia todo
    print("Respawn script desactivado.")
    _G.RespawnScript.Active = false
    disconnectAll()
else
    -- Activa el script
    print("Respawn script activado.")
    _G.RespawnScript.Active = true

    -- ConexiÃ³n para el evento `CharacterAdded`
    local charAddedConnection = LocalPlayer.CharacterAdded:Connect(onCharacterAdded)
    table.insert(_G.RespawnScript.Connections, charAddedConnection)

    -- Configura el personaje actual si ya existe
    if LocalPlayer.Character then
        onCharacterAdded(LocalPlayer.Character)
    end
end
