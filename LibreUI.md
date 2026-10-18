# LibreUI

Biblioteca local de componentes visuais para Roblox, inspirada no painel escuro com contorno verde/RGB. O módulo oferece janelas, abas, controles, avatar em miniatura, prévias 3D, atalhos, resize, temas e notificações. Os componentes são locais: não carregam código de sites, não usam `loadstring`/`HttpGet` e não alteram a partida ou o mundo do jogo.

## Instalação no Roblox Studio

1. Crie um `ModuleScript` chamado `LibreUI` dentro de `ReplicatedStorage`.
2. Cole o conteúdo de [`LibreUI.lua`](./LibreUI.lua) no `ModuleScript`.
3. Crie um `LocalScript` em `StarterPlayer > StarterPlayerScripts`.
4. Faça `require` do módulo nesse `LocalScript` e monte a interface.

`CreateWindow` precisa ser chamado em um `LocalScript`, pois usa `Players.LocalPlayer` e cria a UI em `PlayerGui`. Para publicar no GitHub, envie os dois arquivos desta pasta. No Studio, use o conteúdo do módulo como `ModuleScript`; não execute o arquivo bruto do GitHub com um carregador remoto.

## Exemplo completo

```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LibreUI = require(ReplicatedStorage:WaitForChild("LibreUI"))
local player = Players.LocalPlayer

local Window = LibreUI:CreateWindow({
    Title = "Meu painel",
    Subtitle = "MINHA EXPERIÊNCIA",
    Accent = Color3.fromRGB(40, 225, 151),
    Accent2 = Color3.fromRGB(0, 190, 130),
    Size = UDim2.fromOffset(620, 420),
    Shortcut = Enum.KeyCode.RightShift,
    RGB = false,
    Resizable = true,
    Profile = true, -- mostra nome, @usuário e miniatura no rodapé da sidebar
})

-- Ícone pode ser um ID/URI de imagem do Roblox ou um glifo curto.
local Geral = Window:CreateTab({ Name = "Geral", Icon = "rbxassetid://123456789" })
Geral:AddSection("Preferências", "Os callbacks ficam sob controle do criador da experiência.")

local ToggleDemo = Geral:AddToggle({
    Name = "Ativar demonstração",
    Default = false,
    Callback = function(enabled)
        print("Toggle:", enabled)
    end,
})

local SliderDemo = Geral:AddSlider({
    Name = "Intensidade",
    Min = 0,
    Max = 100,
    Default = 35,
    Step = 5,
    Suffix = "%",
    Callback = function(value)
        print("Slider:", value)
    end,
})

Geral:AddDropdown({
    Name = "Modo",
    Options = { "Suave", "Equilibrado", "Intenso" },
    Default = "Equilibrado",
    Mode = "Menu", -- ou "Cycle" para avançar a opção a cada clique
    Callback = function(value)
        print("Modo:", value)
    end,
})

Geral:AddInput({
    Name = "Apelido",
    Placeholder = "Digite um nome",
    Default = player.DisplayName,
    MaxLength = 24,
    Callback = function(text)
        print("Texto enviado:", text)
    end,
})

Geral:AddButton({
    Name = "Testar notificação",
    Icon = "rbxassetid://123456789",
    Callback = function()
        Window:Notify("Botão pressionado")
    end,
})

Geral:AddCounter({
    Name = "Cliques",
    Default = 0,
    Notify = true,
    Callback = function(total)
        print("Cliques registrados:", total)
    end,
})

Geral:AddToggle({
    Name = "Notificações",
    Default = true,
    Callback = function(enabled)
        Window:SetNotificationsEnabled(enabled)
    end,
})

local Aparencia = Window:CreateTab({ Name = "Aparência", Icon = "rbxassetid://123456789" })
Aparencia:AddToggle({
    Name = "Animar borda RGB",
    Default = false,
    Callback = function(enabled)
        Window:SetRGBEnabled(enabled)
    end,
})
Aparencia:AddThemePicker({ Name = "Cor do tema", Default = "Verde" })

-- Miniatura com rosto, DisplayName e @username.
Aparencia:AddAvatarPreview({ Player = player, Height = 76 })

-- O carrossel aceita formas básicas ou modelos fornecidos pelo próprio jogo.
local character = player.Character or player.CharacterAdded:Wait()
Aparencia:Add3DPreview({
    Name = "Prévia 3D",
    Items = {
        { Name = "Esfera", Shape = Enum.PartType.Ball, Size = Vector3.new(1.7, 1.7, 1.7) },
        { Name = "Bloco", Shape = Enum.PartType.Block, Size = Vector3.new(1.8, 1.5, 1.4) },
        { Name = "Meu avatar", Model = character, RotationY = 180 },
    },
    OnChanged = function(item, index)
        print("Prévia:", item.Name, index)
    end,
    OnSelect = function(item, index)
        print("Selecionado:", item.Name, index)
    end,
})

local Sistema = Window:CreateTab({ Name = "Sistema", Icon = "rbxassetid://123456789" })
Sistema:AddKeybind({
    Name = "Atalho da janela",
    Default = Enum.KeyCode.RightShift,
    BindWindow = true,
    Callback = function(keyCode)
        print("Novo atalho:", keyCode.Name)
    end,
})
Sistema:AddResetButton({ Name = "RESTAURAR PADRÕES" })

-- Botão flutuante opcional para abrir/fechar a janela.
Window:CreateLauncher({ Text = "GG", Position = UDim2.new(0, 18, 0.5, -22) })

-- Os controles retornados permitem ler, alterar e restaurar valores.
ToggleDemo:SetValue(true)
SliderDemo:SetValue(60)
print(ToggleDemo:GetValue(), SliderDemo:GetValue())
Window:Notify("LibreUI pronta", 3)
```

## API rápida

| Chamada | O que faz |
|---|---|
| `LibreUI:CreateWindow(options)` | Cria janela e devolve `Window`. Por padrão mostra a ficha local de perfil, permite resize e usa `RightShift` para abrir/fechar. |
| `Window:CreateTab("Nome")` | Cria uma aba. Também aceita `{Name = "Nome", Icon = 123456789}`. A primeira aba fica selecionada. |
| `Tab:AddSection(título, descrição?)` | Cabeçalho de seção. |
| `Tab:AddLabel({Name, Height?, TextSize?})` | Rótulo de texto simples. |
| `Tab:AddParagraph({Title, Content, Height?})` | Card com título e texto multilinha. |
| `Tab:AddToggle({Name, Default, Callback})` | Toggle; callback recebe `true` ou `false`. |
| `Tab:AddSlider({Name, Min, Max, Default, Step, Suffix, Decimals, Callback})` | Slider numérico. |
| `Tab:AddDropdown({Name, Options, Default, Mode, Callback})` | Dropdown. `Mode = "Menu"` abre lista; `Mode = "Cycle"` avança no clique. |
| `Tab:AddThemePicker({Name, Options, Default, Callback})` | Seletor de presets de tema, com verde, ciano, roxo, rosa e laranja por padrão. |
| `Tab:AddInput(config)` / `Tab:AddTextBox(config)` | Campo de texto; `Live = true` envia callback a cada edição, senão ao perder foco. |
| `Tab:AddButton({Name, Icon, Callback})` | Botão de ação. `Icon` aceita ID/URI de imagem ou glifo. |
| `Tab:AddCounter({Name, Default, Notify, Callback})` | Botão contador com `GetValue`, `SetValue` e `Reset`. |
| `Tab:AddResetButton(config)` | Botão pronto que chama `Window:ResetDefaults()`. |
| `Tab:AddImage({Image, Height, ScaleType})` | Imagem configurável; retorna `SetImage`. |
| `Tab:AddAvatarPreview({Player, UserId, Image, DisplayName, Username})` | Cartão de avatar/rosto com fallback por iniciais. |
| `Tab:Add3DPreview(config)` | Carrossel 3D com setas, ampliação em modal, rotação e botão de seleção. |
| `Window:SetProfile(config)` / `Window:AddProfileCard(config)` | Adiciona/atualiza a ficha de perfil no rodapé da sidebar; passe `false` para removê-la. |
| `Window:SetAccent(color)` / `Window:SetTheme(colors)` | Atualiza o destaque ou as cores do tema. |
| `Window:SetRGBEnabled(bool)` | Liga/desliga animação de cor do contorno e destaque. |
| `Window:SetResizable(boolOrOptions)` | Liga/desliga resize. Opções aceitam `Enabled`, `MinSize` e `MaxSize` (`Vector2`). |
| `Window:SetShortcut(Enum.KeyCode ou nil)` | Define ou desativa o atalho que alterna a janela. `SetToggleKey` é um alias. |
| `Window:SetNotificationsEnabled(bool)` | Ativa/desativa `Window:Notify`. |
| `Window:CreateLauncher({Text, Icon?, Position?, Size?})` | Cria um botão flutuante arrastável que alterna a janela. |
| `Window:ResetDefaults()` | Restaura tema inicial, shortcut e valores padrão dos controles. |
| `Window:Open()`, `Window:Close()`, `Window:Toggle()` | Mostra, oculta ou alterna a janela. `Close` também fecha overlays. |
| `Window:Notify(message, duration?)` | Notificação local; duração padrão de 2,5 segundos. |
| `Window:Destroy()` | Desconecta eventos e remove a interface. |

Controles (toggle, slider, dropdown, input, keybind, contador e preview 3D) retornam objetos com `GetValue()`, `SetValue(...)` e/ou `Reset()`, conforme o tipo.

## Opções de `CreateWindow`

- `Title`, `Subtitle`, `Name`, `Size`, `Position` e `DisplayOrder` configuram texto, tamanho, posição e camada do `ScreenGui`.
- `Accent`, `Accent2`, `Background`, `Surface`, `Surface2`, `Text`, `Muted`, `Off` e `Track` aceitam cores `Color3`.
- `Profile = false` oculta a ficha; uma tabela em `Profile` permite personalizar `Player`, `UserId`, `DisplayName`, `Username`, `Image` e `ShowThumbnail`.
- `RGB = true` inicia o RGB ligado.
- `Shortcut = Enum.KeyCode.RightShift` define o atalho inicial; `Shortcut = false` desativa-o.
- `Resizable = false` desativa resize; também aceita `{Enabled, MinSize = Vector2, MaxSize = Vector2}`. Os limites padrão são `500×340` e `860×680`.
- `Notifications = false` inicia notificações desativadas.

## Itens de `Add3DPreview`

Cada item pode usar `Name`, `Shape`, `Size`, `Color` e `Material` para uma peça básica, ou `Model` apontando para um `Model`/`BasePart` já existente no jogo. `RotationY` (graus) ajusta a orientação; por exemplo, `RotationY = 180` costuma deixar o rosto de um avatar voltado para a câmera. `DefaultIndex`, `CameraDistance`, `ExpandedCameraDistance`, `FieldOfView`, `RotationSpeed`, `ExpandText`, `SelectText`, `OnChanged` e `OnSelect` são opcionais.

O carrossel clona os modelos e os mostra somente em `ViewportFrame`/`WorldModel`; remove scripts/ferramentas do clone e ancora as partes, sem alterar o original, o personagem real, a câmera ou o `Workspace`. O modelo informado deve permitir `:Clone()`.

## Observações

- Componentes e nomes de métodos são locais ao módulo; para adicionar tabs e controles não é necessário carregar uma biblioteca por URL.
- IDs de imagem só são necessários quando se quer mostrar um ícone ou uma imagem personalizada; a maioria dos componentes usa texto e estilos nativos.
- A biblioteca é a camada visual. Use callbacks para a lógica legítima da sua própria experiência e mantenha essa lógica separada do módulo.
