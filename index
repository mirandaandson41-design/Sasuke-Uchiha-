const { Client, GatewayIntentBits, REST, Routes, SlashCommandBuilder } = require('discord.js');

const client = new Client({ intents: [GatewayIntentBits.Guilds] });

let economia = {};

function getUser(id) {
  if (!economia[id]) {
    economia[id] = { carteira: 0, banco: 0 };
  }
  return economia[id];
}

const commands = [
  new SlashCommandBuilder().setName('saldo').setDescription('Ver seu saldo'),
  new SlashCommandBuilder().setName('trabalhar').setDescription('Ganhar dinheiro'),
  new SlashCommandBuilder().setName('daily').setDescription('Recompensa diária'),
  new SlashCommandBuilder()
    .setName('depositar')
    .setDescription('Depositar dinheiro')
    .addIntegerOption(o => o.setName('quantia').setRequired(true)),
  new SlashCommandBuilder()
    .setName('sacar')
    .setDescription('Sacar dinheiro')
    .addIntegerOption(o => o.setName('quantia').setRequired(true)),
  new SlashCommandBuilder()
    .setName('pagar')
    .setDescription('Pagar alguém')
    .addUserOption(o => o.setName('usuario').setRequired(true))
    .addIntegerOption(o => o.setName('quantia').setRequired(true)),
  new SlashCommandBuilder()
    .setName('roubar')
    .setDescription('Tentar roubar alguém')
    .addUserOption(o => o.setName('usuario').setRequired(true)),
].map(cmd => cmd.toJSON());

const rest = new REST({ version: '10' }).setToken(process.env.TOKEN);

(async () => {
  await rest.put(
    Routes.applicationCommands(process.env.CLIENT_ID),
    { body: commands },
  );
})();

client.on('interactionCreate', async interaction => {
  if (!interaction.isChatInputCommand()) return;

  const user = getUser(interaction.user.id);

  if (interaction.commandName === 'saldo') {
    return interaction.reply(`💰 Carteira: ${user.carteira}\n🏦 Banco: ${user.banco}`);
  }

  if (interaction.commandName === 'trabalhar') {
    const ganho = Math.floor(Math.random() * 100) + 50;
    user.carteira += ganho;
    return interaction.reply(`💼 Você trabalhou e ganhou ${ganho}`);
  }

  if (interaction.commandName === 'daily') {
    const ganho = 500;
    user.carteira += ganho;
    return interaction.reply(`🎁 Você recebeu ${ganho} do daily`);
  }

  if (interaction.commandName === 'depositar') {
    const q = interaction.options.getInteger('quantia');
    if (user.carteira < q) return interaction.reply('❌ Dinheiro insuficiente');
    user.carteira -= q;
    user.banco += q;
    return interaction.reply(`🏦 Depositou ${q}`);
  }

  if (interaction.commandName === 'sacar') {
    const q = interaction.options.getInteger('quantia');
    if (user.banco < q) return interaction.reply('❌ Banco insuficiente');
    user.banco -= q;
    user.carteira += q;
    return interaction.reply(`💸 Sacou ${q}`);
  }

  if (interaction.commandName === 'pagar') {
    const alvo = interaction.options.getUser('usuario');
    const q = interaction.options.getInteger('quantia');
    const target = getUser(alvo.id);

    if (user.carteira < q) return interaction.reply('❌ Sem dinheiro');
    user.carteira -= q;
    target.carteira += q;

    return interaction.reply(`💸 Pagou ${q} para ${alvo}`);
  }

  if (interaction.commandName === 'roubar') {
    const alvo = interaction.options.getUser('usuario');
    const target = getUser(alvo.id);

    if (Math.random() > 0.5) {
      const valor = Math.floor(target.carteira * 0.3);
      target.carteira -= valor;
      user.carteira += valor;
      return interaction.reply(`🦹 Roubou ${valor} de ${alvo}`);
    } else {
      return interaction.reply('🚓 Você foi pego!');
    }
  }
});

client.login(process.env.TOKEN);
