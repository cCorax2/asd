using Discord;
using Discord.Commands;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net;
using System.Text;
using System.Threading.Tasks;
using System.Xml.Linq;

namespace Chise1._0.modules
{
    public class MAL : ModuleBase<SocketCommandContext>
    {
        [Command("mal")]
        public async Task MyAnimeListAsync(params string[] args)
        {
            string s = String.Format("https://myanimelist.net/api/anime/search.xml?q={0}", args[0]);

            HttpWebRequest _req = (HttpWebRequest)WebRequest.Create(s);
            _req.ContentType = "application/rss+xml";
            _req.Method = "GET";
            _req.UserAgent = "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/34.0.1847.116 Safari/537.36";
            //_req.Credentials = new NetworkCredential("ChiseChanBot", "SqWeT123Z67");
            string user = "ChiseChanBot";
            string password = "SqWeT123Z67";
            string svcCredentials = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(user + ":" + password));

            _req.Headers.Add("Authorization", "Basic " + svcCredentials);

            HttpWebResponse _response = (HttpWebResponse)_req.GetResponse();
            Console.WriteLine(_response.StatusDescription);
            Stream dataStream = _response.GetResponseStream();
            StreamReader reader = new StreamReader(dataStream);
            string responseFromServer = reader.ReadToEnd();
            Console.WriteLine(responseFromServer);

            //string firstentry = responseFromServer.Substring(responseFromServer.IndexOf("<entry>"), responseFromServer.IndexOf("</entry>"));

            var xml = XElement.Parse(responseFromServer);
            var firstentry = xml.Descendants("entry").FirstOrDefault();

            var title = firstentry.Element("title").Value;
            var episodes = firstentry.Element("episodes").Value;
            //string synopsys = firstentry.Element("synopsys").Value;
            var type = firstentry.Element("type").Value;
            var status = firstentry.Element("status").Value;
            var score = firstentry.Element("score").Value;
            var image = firstentry.Element("image").Value;

            //string synopsys_1 = synopsys.Substring(0, synopsys.Length-50);



            await ReplyAsync(firstentry.ToString());

            var builder = new EmbedBuilder();
            builder.WithTitle(title);
            builder.AddInlineField("Episodes", episodes);
            //builder.AddInlineField("Synopsys", synopsys_1);
            builder.AddInlineField("Type", type);
            builder.AddInlineField("Status", status);
            builder.AddInlineField("Score", score);
            builder.WithThumbnailUrl(image);

            builder.WithColor(Color.Red);
            await Context.Channel.SendMessageAsync("", false, builder);
        }
    }
}
