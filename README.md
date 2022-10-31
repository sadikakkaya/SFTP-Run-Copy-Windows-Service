# SFTP-Run-Copy-Windows-Service
# Add Referans using Renci.SshNet; 

# Add class Program
#region Main
static void Main(string[] args)
        {
            #region App.config Add appSettings to Key
            inetpub = ConfigurationManager.AppSettings["inetpub"];
            inetpubZipDestination = ConfigurationManager.AppSettings["inetpubZipDestination"];
            SFTPDestinationInetpubPath = ConfigurationManager.AppSettings["SFTPDestinationInetpubPath"];
            #endregion
            
            string str = DateTime.Now.ToString().Replace("-", "_").Replace(".", "_").Replace(":", "_") + ".zip";
            string destinationArchiveFileName = inetpubZipDestination + @"\" + str;
            try
            {
                ZipFile.CreateFromDirectory(inetpub, destinationArchiveFileName);
            }
            catch (Exception exception)
            {
                Console.WriteLine("Hata     :" + exception.ToString());
            }
            FileInfo f = new FileInfo(destinationArchiveFileName);
            UploadFile(f, SFTPDestinationInetpubPath);
            File.Delete(f.FullName);
            Console.WriteLine(f.Name + " dosyası başarıyla lokalden silindi.");
            Console.WriteLine("Tüm işlemler tamamlanmıştır...");
        }
  #endregion
  
        private static void UploadFile(FileInfo f, string destinationpath)
        {
            string host = ConfigurationManager.AppSettings["SFTPHost"];
            string username = ConfigurationManager.AppSettings["SFTPKullaniciAdi"];
            string password = ConfigurationManager.AppSettings["SFTPSifre"];
            string str4 = "/home/" + username + "/";
            try
            {
                using (SftpClient client = new SftpClient(host, username, password))
                {
                    client.Connect();
                    client.ChangeDirectory(destinationpath);
                    using (FileStream stream = new FileStream(f.FullName, FileMode.Open))
                    {
                        client.BufferSize = 15000;
                        client.UploadFile(stream, Path.GetFileName(f.FullName), null);
                    }
                }
                Console.ForegroundColor = ConsoleColor.Green;
                Console.WriteLine("Yüklenen dosya           :" + f.Name);
                Console.WriteLine("Yüklenen konum           :" + destinationpath);
                Console.WriteLine("---------------------------------------------------------------------------");
                Console.ForegroundColor = ConsoleColor.White;
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Destination Path         :" + destinationpath);
                Console.WriteLine("File Name                :" + f.FullName);
                Console.WriteLine("Hata                     :" + exception.ToString());
                Console.WriteLine("---------------------------------------------------------------------------");
                Console.ForegroundColor = ConsoleColor.White;
            }
        }

        public static string destinationPath { get; set; }

        public static string inetpub { get; set; }

        public static string inetpubZipDestination { get; set; }

        public static string SFTPDestinationInetpubPath { get; set; }
        
# Add App.config      
        	<appSettings>
            <add key="inetpub" value="C:\YEDEK"/>
            <add key="inetpubZipDestination" value="C:\yedek_archive"/>
            <add key="SFTPDestinationInetpubPath" value="DB/Backup/SQL"/>
            <add key="SFTPHost" value=""/>
            <add key="SFTPKullaniciAdi" value=""/>
            <add key="SFTPSifre" value=""/>
          </appSettings>
