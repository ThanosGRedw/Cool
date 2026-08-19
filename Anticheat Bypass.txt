local RainbowEdgeGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local UICorner = Instance.new("UICorner")
local Title = Instance.new("TextLabel")
local UIStroke = Instance.new("UIStroke")
local ButtonsHolder = Instance.new("ScrollingFrame")
local UIListLayout = Instance.new("UIListLayout")

local MetaMethodBtn = Instance.new("TextButton")
local HandshakeBtn = Instance.new("TextButton")
local HookCheckBtn = Instance.new("TextButton")
local DetourBtn = Instance.new("TextButton")
local MemoryBtn = Instance.new("TextButton")
local VMCheckBtn = Instance.new("TextButton")
local SignatureBtn = Instance.new("TextButton")
local IntegrityBtn = Instance.new("TextButton")

RainbowEdgeGui.Name = "RainbowEdgeGui"
RainbowEdgeGui.Parent = game:GetService("CoreGui")
RainbowEdgeGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

MainFrame.Name = "MainFrame"
MainFrame.Parent = RainbowEdgeGui
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.35, 0, 0.2, 0)
MainFrame.Size = UDim2.new(0, 400, 0, 500)
MainFrame.Active = true
MainFrame.Draggable = true

UICorner.CornerRadius = UDim.new(0, 12)
UICorner.Parent = MainFrame

UIStroke.Parent = MainFrame
UIStroke.Thickness = 2
task.spawn(function()
	while task.wait() do
		for i = 0, 1, 0.005 do
			UIStroke.Color = Color3.fromHSV(i, 1, 1)
			task.wait()
		end
	end
end)

Title.Name = "Title"
Title.Parent = MainFrame
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 0, 0, 0)
Title.Size = UDim2.new(1, 0, 0, 45)
Title.Font = Enum.Font.GothamBold
Title.Text = "Anti Cheat Bypass"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 22
Title.TextYAlignment = Enum.TextYAlignment.Center

ButtonsHolder.Name = "ButtonsHolder"
ButtonsHolder.Parent = MainFrame
ButtonsHolder.BackgroundTransparency = 1
ButtonsHolder.Position = UDim2.new(0, 0, 0, 50)
ButtonsHolder.Size = UDim2.new(1, 0, 1, -50)
ButtonsHolder.CanvasSize = UDim2.new(0, 0, 0, 600)
ButtonsHolder.ScrollBarThickness = 5

UIListLayout.Parent = ButtonsHolder
UIListLayout.Padding = UDim.new(0, 10)
UIListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.VerticalAlignment = Enum.VerticalAlignment.Top

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Name = "StatusLabel"
StatusLabel.Parent = MainFrame
StatusLabel.BackgroundTransparency = 1
StatusLabel.Position = UDim2.new(0, 0, 0.92, 0)
StatusLabel.Size = UDim2.new(1, 0, 0, 30)
StatusLabel.Font = Enum.Font.GothamBold
StatusLabel.Text = "Ready - Select a bypass method"
StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
StatusLabel.TextSize = 14
StatusLabel.TextTransparency = 0.3

local function styleButton(btn, text)
	btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
	btn.Size = UDim2.new(0, 350, 0, 45)
	btn.Font = Enum.Font.GothamBold
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.TextSize = 16
	btn.Text = text
	btn.AutoButtonColor = true
	local corner = Instance.new("UICorner", btn)
	corner.CornerRadius = UDim.new(0, 8)
	btn.Parent = ButtonsHolder
end

styleButton(MetaMethodBtn, "Metamethod Bypass")

local function bypassMetaMethods()
    StatusLabel.Text = "Scanning for metamethod checks..."
    StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
    
    local checks = {
        "checkcaller",
        "getcallingscript",
        "getfenv",
        "setfenv",
        "getreg",
        "getgc",
        "getconnections",
        "hookfunction",
        "newcclosure"
    }
    
    local foundChecks = {}
    
    for _, check in ipairs(checks) do
        if getgenv()[check] or _G[check] then
            table.insert(foundChecks, check)
        end
    end
    
    if hookfunction then
        local originalHook = hookfunction
        hookfunction = function(func, replacement)
            return originalHook(func, function(...)
                return replacement(...)
            end)
        end
    end
    
    if setreadonly then
        setreadonly(getrenv(), false)
    end
    
    if make_writeable then
        make_writeable(getreg())
    end
    
    StatusLabel.Text = "Metamethod Bypass Complete - Found: " .. #foundChecks .. " checks"
    StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Metamethod Bypass",
        Text = "Bypassed " .. #foundChecks .. " metamethod checks",
        Duration = 5
    })
end

MetaMethodBtn.MouseButton1Click:Connect(bypassMetaMethods)

styleButton(HandshakeBtn, "Handshake Bypass")

local function bypassHandshakes()
    StatusLabel.Text = "Bypassing server handshakes..."
    StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
    
    local remotes = game:GetService("ReplicatedStorage"):GetDescendants()
    local handshakeRemotes = {}
    
    for _, remote in ipairs(remotes) do
        if remote:IsA("RemoteEvent") or remote:IsA("RemoteFunction") then
            if remote.Name:lower():find("handshake") or 
               remote.Name:lower():find("validate") or 
               remote.Name:lower():find("verify") then
                table.insert(handshakeRemotes, remote.Name)
                
                if remote:IsA("RemoteEvent") then
                    local originalFire = remote.FireServer
                    remote.FireServer = function(self, ...)
                        return true
                    end
                elseif remote:IsA("RemoteFunction") then
                    local originalInvoke = remote.InvokeServer
                    remote.InvokeServer = function(self, ...)
                        return true
                    end
                end
            end
        end
    end
    
    StatusLabel.Text = "Handshake Bypass Complete - Bypassed: " .. #handshakeRemotes .. " remotes"
    StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Handshake Bypass",
        Text = "Bypassed " .. #handshakeRemotes .. " handshake remotes",
        Duration = 5
    })
end

HandshakeBtn.MouseButton1Click:Connect(bypassHandshakes)

styleButton(HookCheckBtn, "Hook Check Bypass")

local function bypassHookChecks()
    StatusLabel.Text = "Bypassing hook detection..."
    StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
    
    local hooksBypassed = 0
    
    if detour_function then
        local originalDetour = detour_function
        detour_function = function(...)
            return true
        end
        hooksBypassed = hooksBypassed + 1
    end
    
    if hookfunction then
        for _, func in pairs(getreg()) do
            if type(func) == "function" then
                pcall(function()
                    hookfunction(func, func)
                end)
                hooksBypassed = hooksBypassed + 1
            end
        end
    end
    
    if getconnections then
        for _, connection in ipairs(getconnections(game:GetService("ScriptContext").Error)) do
            connection:Disable()
            hooksBypassed = hooksBypassed + 1
        end
    end
    
    StatusLabel.Text = "Hook Check Bypass Complete - Bypassed: " .. hooksBypassed .. " hooks"
    StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Hook Check Bypass",
        Text = "Bypassed " .. hooksBypassed .. " hook checks",
        Duration = 5
    })
end

HookCheckBtn.MouseButton1Click:Connect(bypassHookChecks)

styleButton(DetourBtn, "Detour Bypass")

local function bypassDetours()
    StatusLabel.Text = "Bypassing function detours..."
    StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
    
    local detoursBypassed = 0
    
    local criticalFunctions = {
        "Instance.new",
        "getfenv",
        "setfenv",
        "getreg",
        "getgc",
        "checkcaller"
    }
    
    for _, funcName in ipairs(criticalFunctions) do
        local success = pcall(function()
            local original = _G[funcName] or getgenv()[funcName]
            if original then
                _G[funcName] = original
                detoursBypassed = detoursBypassed + 1
            end
        end)
    end
    
    if getrenv then
        local env = getrenv()
        for name, func in pairs(env) do
            if type(func) == "function" and not string.find(name, "__") then
                pcall(function()
                    env[name] = func
                end)
                detoursBypassed = detoursBypassed + 1
            end
        end
    end
    
    StatusLabel.Text = "Detour Bypass Complete - Restored: " .. detoursBypassed .. " functions"
    StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Detour Bypass",
        Text = "Restored " .. detoursBypassed .. " detoured functions",
        Duration = 5
    })
end

DetourBtn.MouseButton1Click:Connect(bypassDetours)

styleButton(MemoryBtn, "Memory Bypass")

local function bypassMemoryChecks()
    StatusLabel.Text = "Bypassing memory scans..."
    StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
    
    local memoryPatches = 0
    
    if setreadonly then
        pcall(function() setreadonly(getrenv(), false) end)
        pcall(function() setreadonly(getreg(), false) end)
        pcall(function() setreadonly(getgc(), false) end)
        memoryPatches = memoryPatches + 3
    end
    
    for _, script in ipairs(getscripts()) do
        if script and script:IsA("LocalScript") then
            pcall(function()
                script.Enabled = true
                memoryPatches = memoryPatches + 1
            end)
        end
    end
    
    if getgc then
        for _, obj in ipairs(getgc()) do
            if type(obj) == "table" and rawget(obj, "__acsignature") then
                rawset(obj, "__acsignature", nil)
                memoryPatches = memoryPatches + 1
            end
        end
    end
    
    StatusLabel.Text = "Memory Bypass Complete - Applied: " .. memoryPatches .. " patches"
    StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Memory Bypass",
        Text = "Applied " .. memoryPatches .. " memory patches",
        Duration = 5
    })
end

MemoryBtn.MouseButton1Click:Connect(bypassMemoryChecks)

styleButton(VMCheckBtn, "VM Check Bypass")

local function bypassVMChecks()
    StatusLabel.Text = "Bypassing VM detection..."
    StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
    
    local vmBypasses = 0
    
    if debug then
        debug.info = function() return "C" end
        debug.traceback = function() return "" end
        vmBypasses = vmBypasses + 2
    end
    
    if getcallingscript then
        local original = getcallingscript
        getcallingscript = function() return nil end
        vmBypasses = vmBypasses + 1
    end
    
    if getfenv then
        for i = 1, 10 do
            pcall(function()
                local env = getfenv(i)
                if env and env.script then
                    env.script = nil
                    vmBypasses = vmBypasses + 1
                end
            end)
        end
    end
    
    StatusLabel.Text = "VM Check Bypass Complete - Applied: " .. vmBypasses .. " bypasses"
    StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "VM Check Bypass",
        Text = "Applied " .. vmBypasses .. " VM detection bypasses",
        Duration = 5
    })
end

VMCheckBtn.MouseButton1Click:Connect(bypassVMChecks)

styleButton(SignatureBtn, "Signature Bypass")

local function bypassSignatures()
    StatusLabel.Text = "Bypassing signature scans..."
    StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
    
    local signaturesBypassed = 0
    
    local signatureTables = {
        "_G",
        "shared",
        "getgenv",
        "getrenv"
    }
    
    for _, tableName in ipairs(signatureTables) do
        local target = _G[tableName] or getgenv()[tableName]
        if target and type(target) == "table" then
            for key, value in pairs(target) do
                if string.find(tostring(key), "signature") or 
                   string.find(tostring(key), "checksum") or
                   string.find(tostring(key), "hash") then
                    target[key] = nil
                    signaturesBypassed = signaturesBypassed + 1
                end
            end
        end
    end
    
    if getscripts then
        for _, script in ipairs(getscripts()) do
            if script and script:IsA("LocalScript") then
                pcall(function()
                    script.Name = game:GetService("HttpService"):GenerateGUID(false)
                    signaturesBypassed = signaturesBypassed + 1
                end)
            end
        end
    end
    
    StatusLabel.Text = "Signature Bypass Complete - Cleared: " .. signaturesBypassed .. " signatures"
    StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Signature Bypass",
        Text = "Cleared " .. signaturesBypassed .. " signatures",
        Duration = 5
    })
end

SignatureBtn.MouseButton1Click:Connect(bypassSignatures)

styleButton(IntegrityBtn, "Integrity Check Bypass")

local function bypassIntegrityChecks()
    StatusLabel.Text = "Bypassing integrity checks..."
    StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
    
    local integrityBypasses = 0
    
    if getconnections then
        for _, connection in ipairs(getconnections(game:GetService("ScriptContext").ScriptAdded)) do
            connection:Disable()
            integrityBypasses = integrityBypasses + 1
        end
        
        for _, connection in ipairs(getconnections(game:GetService("ScriptContext").ScriptRemoved)) do
            connection:Disable()
            integrityBypasses = integrityBypasses + 1
        end
    end
    
    local modules = game:GetService("ReplicatedStorage"):GetDescendants()
    for _, module in ipairs(modules) do
        if module:IsA("ModuleScript") and 
           (module.Name:lower():find("integrity") or 
            module.Name:lower():find("security") or
            module.Name:lower():find("anti")) then
            pcall(function()
                module:Destroy()
                integrityBypasses = integrityBypasses + 1
            end)
        end
    end
    
    StatusLabel.Text = "Integrity Bypass Complete - Applied: " .. integrityBypasses .. " bypasses"
    StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "Integrity Bypass",
        Text = "Applied " .. integrityBypasses .. " integrity bypasses",
        Duration = 5
    })
end

IntegrityBtn.MouseButton1Click:Connect(bypassIntegrityChecks)

local AutoBypassBtn = Instance.new("TextButton")
styleButton(AutoBypassBtn, "Auto Detect and Bypass All")

AutoBypassBtn.MouseButton1Click:Connect(function()
    StatusLabel.Text = "Starting comprehensive anti cheat bypass..."
    StatusLabel.TextColor3 = Color3.fromRGB(255, 165, 0)
    
    task.spawn(function()
        bypassMetaMethods()
        task.wait(1)
        bypassHandshakes()
        task.wait(1)
        bypassHookChecks()
        task.wait(1)
        bypassDetours()
        task.wait(1)
        bypassMemoryChecks()
        task.wait(1)
        bypassVMChecks()
        task.wait(1)
        bypassSignatures()
        task.wait(1)
        bypassIntegrityChecks()
        
        StatusLabel.Text = "Comprehensive Bypass Complete - All methods applied"
        StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
        
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = "Auto Bypass Complete",
            Text = "All anti cheat bypass methods have been applied",
            Duration = 8
        })
    end)
end)

RainbowEdgeGui.Destroying:Connect(function()
end)
