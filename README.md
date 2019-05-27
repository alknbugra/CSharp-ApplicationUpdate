# CSharp-ApplicationUpdate
Local Application Update System

![update](https://user-images.githubusercontent.com/29266933/58417367-4c475c80-808d-11e9-9aa9-ef91ec4a67e5.PNG)
![update_1](https://user-images.githubusercontent.com/29266933/58417368-4c475c80-808d-11e9-947c-36096b7d739f.PNG)
![update_2](https://user-images.githubusercontent.com/29266933/58417369-4cdff300-808d-11e9-88a9-74bf5fdfa3f8.PNG)
![update_3](https://user-images.githubusercontent.com/29266933/58417370-4cdff300-808d-11e9-971e-c6a2b5c70cca.PNG)

```sh
//güncelleme

        ApplicationDeployment ad;
        UpdateCheckInfo info;
        string ConnectionString = @"Data Source = "your data source"; Integrated Security = false; Initial Catalog = "your catalog"; User Id = "yourid"; Password="yourpassword" ";
        SqlConnection sqlConnection;
        SqlCommand sqlCommand;
        SqlDataReader dataReader;
        string Username = Environment.UserName;
        string MachineName = Environment.MachineName;
        string AppName = System.Reflection.Assembly.GetExecutingAssembly().GetName().Name;
        string KayitTarih = DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss");
        bool makineVarmi = false;

        bool value = false;
        /// ----------------ALGORİTMA -------------------
        /// 1- Güncellemenin olup olmadığını kontrol ediyor.(yoksa bir tepki vermiyor)
        /// 2- Var ise güncellemeleri yüklüyor restart atmadan önce veri tabanına soruyoruz.
        /// 3- Makine ismi bizde kayıtlı mı ?
        /// 4- Kayıtlı ise version bilgisini güncelle
        /// 5- Kayıtlı değil ise yeni kayıt açıp kullanıcıyı kaydet.
        public bool GuncellemeVarmi()
        {
            try
            {
                ad = ApplicationDeployment.CurrentDeployment;
                info = ad.CheckForDetailedUpdate();
                if (info.UpdateAvailable) //Güncelleme varsa devam et.
                {
                    value = true;
                }
                else
                {
                    value = false;
                }
            }
            catch { }
            return value;
        }

        public void guncelle()
        {
            if (value == true)
            {
                ad.Update();
                if (UserKayitlimi(MachineName, Username, AppName))
                {
                    VeritabaniGuncelle();
                }
                else
                {
                    VeritabaniKayit();
                }
                Application.Restart();
            }
        }

        private bool UserKayitlimi(string machinename, string username, string appname)
        {
            using (sqlConnection = new SqlConnection(ConnectionString))
            {
                sqlConnection.Open();
                string Query = "Select pcname from GuncellemeBilgileri where pcname='" + machinename + "'  AND username='" + username + "' AND appname='" + appname + "'";
                using (sqlCommand = new SqlCommand(Query, sqlConnection))
                {
                    dataReader = sqlCommand.ExecuteReader();
                    while (dataReader.Read())
                    {
                        makineVarmi = true;
                    }
                }
                sqlConnection.Close();
            }
            return makineVarmi;
        }
        private void VeritabaniKayit()
        {
            using (sqlConnection = new SqlConnection(ConnectionString))
            {
                sqlConnection.Open();
                string Query = "insert into GuncellemeBilgileri(username,pcname,appname,version,kayittarihi) values (@1,@2,@3,@4,@5)";
                using (sqlCommand = new SqlCommand(Query, sqlConnection))
                {
                    sqlCommand.Parameters.AddWithValue("@1", Username);    //username
                    sqlCommand.Parameters.AddWithValue("@2", MachineName); //pcname
                    sqlCommand.Parameters.AddWithValue("@3", AppName);     //appname
                    sqlCommand.Parameters.AddWithValue("@4", info.AvailableVersion.ToString());  //güncel version
                    sqlCommand.Parameters.AddWithValue("@5", KayitTarih); //kayittarihi
                    sqlCommand.ExecuteNonQuery();
                }
                sqlConnection.Close();
            }
        }
        private void VeritabaniGuncelle()
        {
            using (sqlConnection = new SqlConnection(ConnectionString))
            {
                sqlConnection.Open();
                string Query = "Update  GuncellemeBilgileri  SET version='" + info.AvailableVersion.ToString() + "' , guncellemetarihi='" + KayitTarih + "'  WHERE username='" + Username + "' AND pcname='" + MachineName + "' AND appname='" + AppName + "'";
                using (sqlCommand = new SqlCommand(Query, sqlConnection))
                {
                    sqlCommand.ExecuteNonQuery();
                }
                sqlConnection.Close();
            }
        }
```
