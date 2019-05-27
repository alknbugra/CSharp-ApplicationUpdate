# CSharp-ApplicationUpdate
Local Application Update System

Gif ;

![guncellme](https://user-images.githubusercontent.com/29266933/58417635-15257b00-808e-11e9-9144-fb7bbce56aa5.gif)

#Example
------------
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

```
#Using
------
```sh

        bool durum = false;
        private void login_VisibleChanged(object sender, EventArgs e)
        {
            database = new database();
            if (database.GuncellemeVarmi())
            {
                pictureBox4.Image = Properties.Resources.red_circle_md;
                label4.Text = "Yeni Güncelleme Mevcut";
                durum = true;
            }
            else
            {
                pictureBox4.Image = Properties.Resources.green_circle_md;
                label4.Text = "Versiyonunuz güncel";
                durum = false;
            }
        }

            private void pictureBox4_Click(object sender, EventArgs e)
        {
            if (durum == true)
            {
                DialogResult dialogResult = MessageBox.Show("Yeni versiyon mevcut yüklemek istediğinize emin misiniz ?", "Yüklemek                      istermisiniz ?", MessageBoxButtons.YesNo, MessageBoxIcon.Question);
                if (dialogResult == DialogResult.Yes)
                {
                    database.guncelle();
                }
            }
            else
            {
                dialogInformation = new DialogInformation();
                dialogInformation.pictureBox1.Image = Properties.Resources.icons8_info_96;
                dialogInformation.label1.Text = "Versiyonunuz güncel durumda.";
                dialogInformation.panel2.BackColor = Color.DodgerBlue;
                dialogInformation.ShowDialog();
            }
        }
```
