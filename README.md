-- CONFIGURAÇÃO
local keyValida = "mecsaultpainel"
local webhook = "https://discordapp.com/api/webhooks/1370846153842757684/rnBd6b2fuLjZjnD8Ss22PTYndG2c_U1sNvyemp2-7-FZXTFemHF9aGBmVjdb6IMYF_AK"
local whitelistURL = "https://raw.githubusercontent.com/sillageeee/whitelistttttt/main/README.md"

-- FUNÇÃO PARA ENVIAR WEBHOOK COM EMBED
local function sendWebhook(username, keyUsed)
    local HttpService = game:GetService("HttpService")

    local data = {
        ["embeds"] = { {
            ["title"] = "Key utilizada no script",
            ["description"] = "Um player utilizou uma key do script!",
            ["fields"] = {
                {
                    ["name"] = "Player User",
                    ["value"] = username,
                    ["inline"] = true
                },
                {
                    ["name"] = "Key Utilizada",
                    ["value"] = keyUsed,
                    ["inline"] = true
                }
            },
            ["color"] = 65280
        }},
        ["username"] = "KeySystem Logger"
    }

    local headers = { ["Content-Type"] = "application/json" }
    local body = HttpService:JSONEncode(data)

    local request = (syn and syn.request) or (http and http.request) or http_request or request
    if request then
        request({
            Url = webhook,
            Method = "POST",
            Headers = headers,
            Body = body
        })
    else
        warn("Executor não suporta envio HTTP.")
    end
end

-- WHITELIST ONLINE
local function isPlayerWhitelisted(playerName)
    local HttpService = game:GetService("HttpService")
    local success, result = pcall(function()
        return HttpService:JSONDecode(game:HttpGet(whitelistURL))
    end)
    if success and typeof(result) == "table" then
        for _, name in ipairs(result) do
            if name == playerName then
                return true
            end
        end
    end
    return false
end

-- GUI E VERIFICAÇÃO
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local screenGui = Instance.new("ScreenGui")
screenGui.Parent = game.CoreGui

local inputBox = Instance.new("TextBox")
inputBox.Parent = screenGui
inputBox.Size = UDim2.new(0, 400, 0, 50)
inputBox.Position = UDim2.new(0.5, -200, 0.5, -25)
inputBox.PlaceholderText = "Digite a chave..."
inputBox.Text = ""
inputBox.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
inputBox.TextColor3 = Color3.fromRGB(255, 255, 255)
inputBox.TextSize = 20

inputBox.FocusLost:Connect(function()
    local enteredKey = inputBox.Text

    if not isPlayerWhitelisted(LocalPlayer.Name) then
        inputBox.Text = "❌ Você não está na whitelist!"
        task.wait(1.5)
        inputBox.Text = ""
        return
    end

    if enteredKey == keyValida then
        inputBox.Text = "✔️ Key válida!"
        sendWebhook(LocalPlayer.Name, enteredKey)
        task.wait(1)
        inputBox:Destroy()

        -- 🔓 Script principal liberado
        loadstring(game:HttpGet("https://raw.githubusercontent.com/sillageeee/awdnawdadad/refs/heads/main/sla.lua"))()

    else
        inputBox.Text = "❌ Key inválida"
        task.wait(1.5)
        inputBox.Text = ""
    end
end)
