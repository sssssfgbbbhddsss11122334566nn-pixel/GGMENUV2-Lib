# LibreUI

Biblioteca local de componentes visuais para Roblox, com tema escuro, contorno verde, abas, toggles, sliders, dropdowns, botões e notificações. O arquivo não carrega scripts de sites, não usa `loadstring` e não altera o mundo do jogo.

## Instalação no Roblox Studio

1. Crie um `ModuleScript` chamado `LibreUI` dentro de `ReplicatedStorage`.
2. Cole o conteúdo de [`LibreUI.lua`](./LibreUI.lua) nesse `ModuleScript`.
3. Crie um `LocalScript` em `StarterPlayer > StarterPlayerScripts`.
4. Faça `require` do módulo no `LocalScript` e monte sua interface.

`CreateWindow` exige um `LocalScript`, pois usa `Players.LocalPlayer` e desenha a interface em `PlayerGui`.

## Exemplo completo

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LibreUI = require(ReplicatedStorage:WaitForChild("LibreUI"))

local Window = LibreUI:CreateWindow({
    Title = "Meu painel",
    Subtitle = "MINHA EXPERIÊNCIA",
    Accent = Color3.fromRGB(40, 225, 151),
    Size = UDim2.fromOffset(620, 420),
})

local Geral = Window:CreateTab("Geral")
Geral:AddSection("Preferências", "Exemplo de controles locais")

local ToggleDemo = Geral:AddToggle({
    Name = "Ativar opção de demonstração",
    Default = false,
    Callback = function(ativo)
        print("Toggle:", ativo)
    end,
})

local SliderDemo = Geral:AddSlider({
    Name = "Intensidade",
    Min = 0,
    Max = 100,
    Default = 35,
    Step = 5,
    Suffix = "%",
    Callback = function(valor)
        print("Slider:", valor)
    end,
})

Geral:AddDropdown({
    Name = "Modo",
    Options = { "Suave", "Equilibrado", "Intenso" },
    Default = "Equilibrado",
    Callback = function(valor)
        print("Modo:", valor)
    end,
})

Geral:AddButton({
    Name = "Testar notificação",
    Callback = function()
        Window:Notify("Botão pressionado")
    end,
})

local OutraAba = Window:CreateTab("Outra aba")
OutraAba:AddLabel({ Name = "Conteúdo da segunda aba" })

-- Os controles retornam objetos com métodos para ler e atualizar valores.
ToggleDemo:SetValue(true)
print(ToggleDemo:GetValue())

SliderDemo:SetValue(60)
print(SliderDemo:GetValue())

-- Métodos da janela:
-- Window:Open()
-- Window:Close()
-- Window:Toggle()
-- Window:Notify("Mensagem", 3) -- duração em segundos
-- Window:Destroy()
```

## API rápida

| Chamada | Uso |
|---|---|
| `LibreUI:CreateWindow(opções)` | Cria uma janela e devolve o objeto `Window`. |
| `Window:CreateTab(nome)` | Cria uma aba e devolve o objeto `Tab`. A primeira aba criada fica selecionada. |
| `Tab:AddSection(título, descrição?)` | Adiciona um cabeçalho de seção. |
| `Tab:AddLabel({Name = "..."})` | Adiciona um rótulo simples. |
| `Tab:AddToggle({Name, Default, Callback})` | Adiciona um toggle; o callback recebe `true` ou `false`. |
| `Tab:AddSlider({Name, Min, Max, Default, Step, Suffix, Callback})` | Adiciona um slider; o callback recebe o número atualizado. `Decimals` é opcional. |
| `Tab:AddDropdown({Name, Options, Default, Callback})` | Adiciona um seletor que percorre as opções a cada clique. |
| `Tab:AddButton({Name, Callback})` | Adiciona um botão de ação. |
| `Window:Notify(mensagem, duração?)` | Exibe uma notificação local. A duração padrão é 2,5 segundos. |
| `Window:Open()`, `Window:Close()`, `Window:Toggle()` | Mostra, esconde ou alterna a janela. |
| `Window:Destroy()` | Desconecta os eventos da biblioteca e remove a interface. |

### Opções de `CreateWindow`

- `Title`: título da janela.
- `Subtitle`: subtítulo.
- `Name`: nome do `ScreenGui`.
- `Size`: tamanho em `UDim2`.
- `Position`: posição em `UDim2` (padrão: centro da tela).
- `Accent`: cor de destaque em `Color3`.
- `Background`, `Surface`, `Surface2`, `Text`, `Muted`, `Off`: cores opcionais do tema.
- `DisplayOrder`: ordem visual do `ScreenGui`.

## Observações

- Os nomes dos métodos são chamados diretamente no módulo, janela e aba; não é necessário usar IDs de imagens ou assets para esses controles.
- O `Dropdown` fornecido é um seletor compacto: cada clique avança para a próxima opção.
- A biblioteca é apenas a camada visual. Use os callbacks para lógica legítima da sua própria experiência e mantenha qualquer lógica de jogo separada do módulo.
- Para compartilhar no GitHub, envie `LibreUI.lua` e este README ao seu repositório. No Roblox Studio, use o conteúdo do módulo como `ModuleScript`; não execute o arquivo bruto do GitHub com um carregador remoto.
