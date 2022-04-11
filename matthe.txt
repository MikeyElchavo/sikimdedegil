
const Discord = require("discord.js")
const client = new Discord.Client()
const ayarlar = require("./ayarlar.json")
const moment = require("moment")//Matthe#0001
const fs = require("fs")
const db = require("quick.db")
const chalk = require("chalk")
require('./util/Loader.js')(client)
const http = require("http");
const express = require("express");
const app = express();
client.on("message", message => {
  const dmchannel = client.channels.find("name", "dm");
  if (message.channel.type === "dm") {
    if (message.author.id === client.user.id) return;
    dmchannel.sendMessage("", {
      embed: {
        color: 3447003,
        title: `DM Atan Kişi: **${message.author.tag}**`,
        description: `Dm Mesajı: **${message.content}**`
      }
    });
  }
  if (message.channel.bot) return;
});

client.commands = new Discord.Collection()
client.aliases = new Discord.Collection()
fs.readdir('./commands/', (err, files) => { 
  if (err) console.error(err);               
  console.log(`${files.length} komut yüklenecek.`)//Youtube Matthe
  files.forEach(f => {                    
    let props = require(`./commands/${f}`)
    console.log(`${props.config.name} komutu yüklendi.`)
    client.commands.set(props.config.name, props)
    props.config.aliases.forEach(alias => {       
      client.aliases.set(alias, props.config.name)
    });
  });
})
//rolkoruma//
client.on("roleDelete", async role => {
  let rolkoruma = await db.fetch(`rolk_${role.guild.id}`);
  if (rolkoruma) { 
         const entry = await role.guild.fetchAuditLogs({ type: "ROLE_DELETE" }).then(audit => audit.entries.first());
    if (entry.executor.id == client.user.id) return;
  role.guild.roles.create({ data: {
          name: role.name,
          color: role.color,
          hoist: role.hoist,
          permissions: role.permissions,
          mentionable: role.mentionable,
          position: role.position
}, reason: 'Silinen Roller Tekrar Açıldı.'})
  }
})

//

client.on("roleCreate", async role => {
  let rolkorumaa = await db.fetch(`rolk_${role.guild.id}`);
  if (rolkorumaa) { 
       const entry = await role.guild.fetchAuditLogs({ type: "ROLE_CREATE" }).then(audit => audit.entries.first());
    if (entry.executor.id == client.user.id) return;
  role.delete()
  }
})



client.on('message', async message => {
  
  if(message.content === '.tag') {
    message.channel.send(`\`${ayarlar.tag}\``)//Youtube Matthe
  }
  })


client.on("ready", () => {
    console.log(chalk.redBright(`tm`))
})





// BOTUN İNTENTLERİNİ AÇMAYI UNUTMAYIN 
client.on("guildMemberAdd", member => {
    require("moment-duration-format")
      var üyesayısı = member.guild.members.cache.size.toString().replace(/ /g, "    ")
      var üs = üyesayısı.match(/([0-9])/g)
      üyesayısı = üyesayısı.replace(/([a-zA-Z])/g, "bilinmiyor").toLowerCase()
      if(üs) {
        üyesayısı = üyesayısı.replace(/([0-9])/g, d => {
          return {
            '0': `0`,
            '1': `1`,
            '2': `2`,
            '3': `3`,
            '4': `4`, // BOTUN OLDUĞU SUNUCUDA OLMA ŞARTI İLE HARAKETLİ EMOJİDE KOYABİLİRSİNİZ!
            '5': `5`,
            '6': `6`,
            '7': `7`,
            '8': `8`,
            '9': `9`}[d];})}
    const kanal = member.guild.channels.cache.find(r => r.id === (ayarlar.hosgeldinKanal)); 
    let user = client.users.cache.get(member.id);//Youtube BoranGkdn
    require("moment-duration-format");
      const kurulus = new Date().getTime() - user.createdAt.getTime();  
     const gecen = moment.duration(kurulus).format(` YY **[Yıl]** DD **[Gün]** HH **[Saat]** mm **[Dakika,]**`) 
    var kontrol;
  if (kurulus < 1296000000) kontrol = `Sohbete katılmak için hesabın çok genç! :x: `
  
  if (kurulus > 1296000000) kontrol = `Hesabın Sohbete Katılmak İçin Uygun Gözüküyor! :ballot_box_with_check: `
  
    moment.locale("tr");
  member.roles.add(ayarlar.kayıtsızRol)
  
//Youtube BoranGkdn
  
    kanal.send(`
Sunucumuza hoş geldin, <@`+ member + `>! Sayende sunucumuz **`+üyesayısı+`** kişi. 
    

`+kontrol+`
    
  Sohbete katılabilirsin dostum!
    
Ceza işlemlerin <#947112896360484935> kanalını okuduğun varsayılarak uygulanır.`)});


client.login(process.env.TOKEN)


//Youtube Matthe
//----------------------------------------------------- TAG ROL ------------------------------------------------\\

// tag rol kodu bana ait değildir, geliştirip sizlere sundum.
client.on("userUpdate", async function(oldUser, newUser) { 
    const guildID = "947107463046516796"// sunucu ıd
    const roleID = "947137816545067008"// taglı rolünüzün ıd
    const tag = "神"// tagınız
    const chat = '947112924441346098'// chat kanalı ıd
    const taglog = '947112924441346098' // log kanalı ıd
  
    const guild = client.guilds.cache.get(guildID)
    const role = guild.roles.cache.find(roleInfo => roleInfo.id === roleID)
    const member = guild.members.cache.get(newUser.id)
    const embed = new Discord.MessageEmbed().setAuthor(member.displayName, member.user.avatarURL({ dynamic: true })).setColor('#ff0010').setTimestamp().setFooter('Senju Kami');
    if (newUser.username !== oldUser.username) {
        if (oldUser.username.includes(tag) && !newUser.username.includes(tag)) {
            member.roles.remove(roleID)
            client.channels.cache.get(taglog).send(embed.setDescription(`${newUser} Kullanıcısı tagımızı çıkardığı için taglı rolü alındı!`))
        } else if (!oldUser.username.includes(tag) && newUser.username.includes(tag)) {
            member.roles.add(roleID)
            client.channels.cache.get(chat).send(`**Mükemmel! ${newUser} Tagımızı alarak çetemize katıldı!**`)
            client.channels.cache.get(taglog).send(embed.setDescription(`${newUser} Kullanıcısı tagımızı aldığı için taglı rolü verildi!`))
        }
    }
   if (newUser.discriminator !== oldUser.discriminator) {
        if (oldUser.discriminator == "" && newUser.discriminator !== "") {
            member.roles.remove(roleID)
            client.channels.cache.get(taglog).send(embed.setDescription(`${newUser} Kullanıcısı etiket tagımızı çıkardığı için taglı rolü alındı!`))
        } else if (oldUser.discriminator !== "" && newUser.discriminator == "") {
            member.roles.add(roleID)-
            client.channels.cache.get(taglog).send(embed.setDescription(`${newUser} Kullanıcısı etiket tagımızı aldığı için taglı rolü verildi!`))
            client.channels.cache.get(chat).send(`**Mükemmel! ${newUser} Etiket tagımızı alarak çetemize katıldı!**`)
        }
    }
  
  })
client.on("userUpdate", async function(oldUser, newUser) { 
    const guildID = "947107463046516796"// sunucu ıd
    const roleID = "947137816545067008"// taglı rolünüzün ıd
    const tag = "Brahman"// tagınız
    const chat = '947112924441346098'// chat kanalı ıd
    const taglog = '947112924441346098' // log kanalı ıd
  
    const guild = client.guilds.cache.get(guildID)
    const role = guild.roles.cache.find(roleInfo => roleInfo.id === roleID)
    const member = guild.members.cache.get(newUser.id)
    const embed = new Discord.MessageEmbed().setAuthor(member.displayName, member.user.avatarURL({ dynamic: true })).setColor('#ff0010').setTimestamp().setFooter('Senju Kami');
    if (newUser.username !== oldUser.username) {
        if (oldUser.username.includes(tag) && !newUser.username.includes(tag)) {
            member.roles.remove(roleID)
            client.channels.cache.get(taglog).send(embed.setDescription(`${newUser} Kullanıcısı tagımızı çıkardığı için taglı rolü alındı!`))
        } else if (!oldUser.username.includes(tag) && newUser.username.includes(tag)) {
            member.roles.add(roleID)
            client.channels.cache.get(chat).send(`**Mükemmel! ${newUser} Tagımızı alarak çetemize katıldı!**`)
            client.channels.cache.get(taglog).send(embed.setDescription(`${newUser} Kullanıcısı tagımızı aldığı için taglı rolü verildi!`))
        }
    }
   if (newUser.discriminator !== oldUser.discriminator) {
        if (oldUser.discriminator == "0034" && newUser.discriminator !== "0034") {
            member.roles.remove(roleID)
            client.channels.cache.get(taglog).send(embed.setDescription(`${newUser} Kullanıcısı etiket tagımızı çıkardığı için taglı rolü alındı!`))
        } else if (oldUser.discriminator !== "0034" && newUser.discriminator == "0034") {
            member.roles.add(roleID)-
            client.channels.cache.get(taglog).send(embed.setDescription(`${newUser} Kullanıcısı etiket tagımızı aldığı için taglı rolü verildi!`))
            client.channels.cache.get(chat).send(`**Mükemmel! ${newUser} Etiket tagımızı alarak çetemize katıldı!**`)
        }
    }
  
  })

client.on("guildMemberAdd", member => {
  var moment = require("moment")
  require("moment-duration-format")
  moment.locale("tr")
   var {Permissions} = require('discord.js');
   var x = moment(member.user.createdAt).add(15, 'days').fromNow()
   var user = member.user
   x = x.replace("birkaç saniye önce", " ")
   if(!x.includes("önce") || x.includes("sonra") ||x == " ") {
   var rol = member.guild.roles.cache.get("955244543312285726") //Cezalı Rol İD
   var kayıtsız = member.guild.roles.cache.get("947112656052060241") //Alınacak Rol İD
   member.roles.add(rol)
member.user.send('Hesabın 15 günden önce açıldığı için cezalıya atıldın! Açtırmak İçin Yetkililere Bildir.')
setTimeout(() => {

        member.roles.remove(ayarlar.uyeRol);

}, 1000)

  
    
   }
        else {

        }  
    });

client.on("guildMemberAdd", (member) => {

  // Sunucuya Giren Kişiye Rol Verme
  member.roles.add(ayarlar.uyeRol);

  // Hg Mesajı
  client.channels.cache
    .get("947112982339526687")
    .send(`${member} **Kişisine <@&${ayarlar.uyeRol}> Rolünü Verdim**`);
});

client.on("guildMemberAdd", async (member) => {
  let csdb = require("croxydb");
  let data = csdb.get("csotorol." + member.guild.id);

  if (data) {
    let rol = member.guild.roles.cache.get(data);
    if (rol) {
      if (!member.user.bot) {
        await member.roles.add(ayarlar.uyeRol);
      }
    }
  }
});

//----------------------------------------------------- TAG ROL ------------------------------------------------\\