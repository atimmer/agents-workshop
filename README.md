# Praktisch aan de slag met AI-agents

Workshop van Anton Timmermans op WordCamp Nederland 2026 (vrijdag 13 november).

In deze workshop zet je je eigen AI-agents op je laptop op. Elke agent is een eigen map met eigen instructies en een eigen geheugen. Je kiest uit twee voorbeelden:

- **WordPress-site-agent**: bekijkt je site van buitenaf (SEO, toegankelijkheid, veiligheid, snelheid, volgens [The Website Specification](https://specification.website)). Als je wilt, kijkt hij ook van binnenuit mee in je berichten en pagina's.
- **E-mailassistent**: helpt je je inbox op te ruimen en stelt antwoorden voor. Werkt het best met Gmail of Google Workspace.

Je hoeft niet te kunnen programmeren. De agent doet het werk en vraagt je om toestemming als dat nodig is.

## Wat heb je nodig

- Een laptop met Windows, macOS of Linux.
- Een betaald abonnement:
  - **Claude Pro** (of hoger): aanbevolen model Claude Opus 5.5.
  - **ChatGPT Plus** (of hoger): aanbevolen model GPT-6.1 Sol.

## Zo begin je

1. **Installeer T3 Code** via https://t3.codes, of via [GitHub Releases](https://github.com/pingdotgg/t3code/releases).
   - Windows: `winget install T3Tools.T3Code`
   - Mac: `brew install --cask t3-code`
2. **Open T3 Code** en volg *Set up T3 Code*. Bij **Connect your agents** klik je bij **Claude Code** of **Codex** op **Install** en daarna op **Sign in**.
   - Bij Claude opent er een venster met een commando: druk op Enter.
   - Daarna log je in de browser in met je Claude- of ChatGPT-account.
   - Windows met Claude: vraagt Claude Code om Git, installeer dan eerst Git via https://git-scm.com/download/win.
3. **Start een nieuw gesprek.**
   - Zie je *What should we work on?*: klik op **Start without a project**.
   - Zie je die knop niet: klik op **Add project** → **Local folder** en kies je persoonlijke map (je thuismap).
4. **Kies het model** onder het tekstvak: *Claude Opus 5.5* (Claude) of *GPT-6.1 Sol* (Codex), met *medium* redeneren.
   - Tip: zet daar ook de toegang op **Full access**. Dan hoef je minder vaak op *Approve* te klikken. De agent vraagt je nog steeds eerst om toestemming voordat hij iets installeert.
5. **Plak dit bericht** en druk op Enter:

   ```text
   Voer de stappen uit in https://github.com/atimmer/agents-workshop/blob/main/setup.md
   ```

6. **Beantwoord de vragen van de agent.** De agent vraagt steeds één ding tegelijk. Vraagt hij toestemming om iets uit te voeren? Lees kort wat het is en klik op **Approve**.

Gaat er iets mis, of heb je T3 Code opnieuw opgestart? Start een nieuw gesprek en plak hetzelfde bericht nog een keer. De agent slaat over wat al klaar is.

## Na afloop

Elke agent staat als eigen project in de zijbalk van T3 Code. Klik met de rechtermuisknop op het project → **New thread** en stel je vraag. Je agents staan in de map `agents` in je thuismap. Elke agent heeft een eigen Git-geschiedenis, en als je wilt ook een privé-back-up op GitHub.

## Voor de nieuwsgierigen

- [`setup.md`](setup.md): de stappen voor de agent (Engels)
- [`templates/`](templates/): de startbestanden per agent
- [`docs/connectors.md`](docs/connectors.md): hoe de koppelingen werken, en wat nog niet getest is
- [`docs/icons.md`](docs/icons.md): de stijlen voor agent-iconen

Licentie: MIT.
