# Exemplo de Modding para Duckov

_Documentação de exemplo de mods para "Escape From Duckov"._

[APIs Notáveis](Documents/NotableAPIs_CN.md)

## Sobre o Harmony

O jogo atualmente não possui Harmony integrado. Foi observado que há conflitos ao carregar diferentes versões do Harmony simultaneamente. Você pode usar como referência as versões do Harmony mais populares na comunidade para desenvolvimento, como a versão 2.4.1 que era a mais recente no momento da redação deste documento.

## Visão Geral do Funcionamento

O sistema de Mods de "Escape From Duckov" automaticamente escaneia e lê a pasta Duckov_Data/Mods, bem como as pastas de itens inscritos na Steam Workshop. Quando o escaneamento detecta que essas pastas contêm arquivos dll específicos, info.ini e preview.png, o mod pode ser gerenciado e carregado no menu de Mods do jogo.
Nota: No sistema Mac, as pastas relevantes estão localizadas em Duckov/Duckov.app/**Contents/Mods/**.

### Regra 1
O jogo lerá o parâmetro **name** no arquivo info.ini da pasta do mod e o usará como namespace para tentar carregar uma classe chamada ModBehaviour. Por exemplo, se o info.ini contiver `name=MyMod`, ele carregará `MyMod.ModBehaviour` do arquivo `MyMod.dll`.

### Regra 2
A classe ModBehaviour do mod deve herdar de **Duckov.Modding.ModBehaviour**, que é uma classe que herda do MonoBehaviour do Unity e contém algumas funcionalidades adicionais necessárias para o sistema de mods. Ao carregar um mod, "Escape From Duckov" criará um GameObject para esse mod e criará uma instância do ModBehaviour chamando GameObject.AddComponent(Type). Os autores de mods podem implementar funcionalidades personalizadas escrevendo eventos do Unity como Start\Update no ModBehaviour, ou registrando outros eventos em "Escape From Duckov".

## Estrutura da Pasta do Mod

Coloque a pasta do Mod preparada em Duckov_Data/Mods do jogo base "Escape From Duckov", e você poderá carregar o Mod na interface de Mods do menu principal do jogo.
Usando "MyMod" como exemplo acima, a estrutura de arquivos publicada deve ser a seguinte:

- MyMod (pasta do mod)
  - MyMod.dll (lógica de funcionalidade personalizada do mod)
  - info.ini (importante, arquivo de configuração do mod)
  - preview.png (imagem de visualização quadrada, resolução recomendada `256*256`)

[Exemplo de Pasta de Mod](DisplayItemValue/ReleaseExample/DisplayItemValue/)

### Detalhes do info.ini

Este arquivo de configuração deve conter os seguintes parâmetros (não é recomendado armazenar outros dados adicionais):

- name (nome do mod, usado principalmente para carregar o arquivo dll. Veja detalhes na _Regra 1_ acima)
- displayName (nome exibido)
- description (descrição exibida)
- publishedFileId (_pode conter_. ID deste Mod na Steam Workshop)
- tags (Tags exibidas na Workshop, separadas por vírgulas)

**Nota: Ao fazer upload para a Steam Workshop, o info.ini será sobrescrito. Alguns dados no arquivo de configuração podem ser perdidos. Por isso, não é recomendado armazenar informações além dos itens acima no info.ini.**

#### Parâmetros disponíveis para Tags

- Weapon
- Equipment & Gear
- Loot & Economy
- Quality of Life
- Cheats & Exploits
- Visual Enhancements
- Sound
- Quest & Progression
- Companion & NPC
- Collectibles
- Gameplay
- Multiplayer & Co-op
- Utility
- Medical & Survival

## Configurando o Projeto C#

**Nota: Ao fazer upload para a Steam Workshop, o info.ini será sobrescrito. As informações originais no info.ini podem ser perdidas. Por isso, não é recomendado armazenar informações além dos itens acima no info.ini.**

## Configurando o Projeto C# / Configuring C# Project

1. Prepare o jogo base "Escape From Duckov" no seu computador.
2. Crie uma Biblioteca de Classes (Class Library) .NET através do Visual Studio.
3. Configure os parâmetros do projeto.
   1) Framework (Target Framework)
      - **É recomendado definir o TargetFramework como .NET Standard 2.1**.
      - Lembre-se de remover recursos não suportados pelo TargetFramework, como `<ImplicitUsings>`
   2) Adicionar Referências (Reference Include)
      - Adicione `\Duckov_Data\Managed\*.dll` de "Escape From Duckov" às referências.
      - Exemplo:
      ```
        <ItemGroup>
          <Reference Include="$(DuckovPath)\Duckov_Data\Managed\TeamSoda.*" />
          <Reference Include="$(DuckovPath)\Duckov_Data\Managed\ItemStatsSystem.dll" />
          <Reference Include="$(DuckovPath)\Duckov_Data\Managed\Unity*" />
        </ItemGroup>
      ```

4. Configuração do projeto concluída! Agora escreva uma classe ModBehaviour no Namespace do seu projeto de Mod.
5. Compile o projeto para obter a dll principal do seu mod. Em seguida, organize a estrutura de pastas conforme as regras descritas acima e você pode começar a testar localmente.

[Exemplo de arquivo csproj](DisplayItemValue/DisplayItemValue.csproj)

Nota: Se encontrar problemas com caminhos de arquivo sob .csproj que não são reconhecidos, você pode abrir o arquivo com uma IDE de terceiros (como VS Code), alterar a codificação do arquivo de UTF-8 with BOM para UTF-8 (sem BOM) e salvar novamente. Depois, ao abrir com o Visual Studio, deve funcionar normalmente.

## Outros

### Unity Package

Ao desenvolver usando Unity, você pode consultar o [arquivo manifest.json](UnityFiles/manifest.json) incluído neste repositório para selecionar packages.

### Itens de Jogo Personalizados

```
// Adicionar item personalizado
ItemStatsSystem.ItemAssetsCollection.AddDynamicEntry(Item prefab)

// Remover item personalizado
ItemStatsSystem.ItemAssetsCollection.RemoveDynamicEntry(Item prefab)
```

- O prefab do item personalizado precisa ter o TypeID configurado. Este ID deve evitar conflitos com o jogo base e outros MODs.
- Se o MOD correspondente não for carregado ao entrar no jogo, os itens personalizados no save desaparecerão diretamente.

### Localização

```
// Sobrescrever texto de localização
SodaCraft.Localizations.LocalizationManager.SetOverrideText(string key, string value)

// Lidar com lógica ao trocar idioma
SodaCraft.Localizations.LocalizationManager.OnSetLanguage:System.Action<SystemLanguage>
```

## Diretrizes da Comunidade Duckov

Para o desenvolvimento saudável e harmonioso de longo prazo da comunidade Duckov, precisamos manter juntos um bom ambiente de criação. Portanto, esperamos que todos sigam as seguintes regras:
1. É proibido conteúdo que viole as leis da equipe de desenvolvimento e da região onde a plataforma Steam está localizada, conteúdo político, pornográfico obsceno ou que promova violência e terror.
2. É proibido conteúdo que insulte gravemente personagens ou distorça o enredo, que pretenda causar aversão e criar divisão na comunidade de jogadores, ou que envolva eventos atuais populares e figuras da vida real que possam causar controvérsias.
3. É proibido conteúdo que use recursos de jogo protegidos por direitos autorais ou outros materiais de terceiros sem autorização.
4. É proibido usar Mods para direcionar a anúncios, doações ou outros links externos de natureza comercial ou não oficial, ou conduzir outras pessoas a pagarem.
5. Mods que usam conteúdo de IA precisam ser rotulados.  
Para Mods publicados na Steam Workshop, se violarem as regras acima, podemos excluí-los diretamente sem aviso prévio e podemos banir as permissões dos criadores relacionados.
