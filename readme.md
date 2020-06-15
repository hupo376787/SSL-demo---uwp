
A ssl demo.

The main code is here:

```
protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            //StorageFile certificateFile = await Package.Current.InstalledLocation.GetFileAsync(@"client.p12");
            //IBuffer certificateBuffer = await FileIO.ReadBufferAsync(certificateFile);
            //string encodedCertificate = Windows.Security.Cryptography.CryptographicBuffer.EncodeToBase64String(certificateBuffer);
            //await CertificateEnrollmentManager.ImportPfxDataAsync(encodedCertificate, "000000", ExportOption.NotExportable, KeyProtectionLevel.NoConsent, InstallOptions.None, "Client Certificate");

            var handler = new HttpClientHandler();
            handler.ClientCertificateOptions = ClientCertificateOption.Manual;
            X509Certificate2 cer = new X509Certificate2(File.ReadAllBytes("client.pfx"), "000000");
            handler.ClientCertificates.Add(cer);
            handler.SslProtocols = System.Security.Authentication.SslProtocols.Tls12 | System.Security.Authentication.SslProtocols.Tls11 | System.Security.Authentication.SslProtocols.Tls;
            handler.ServerCertificateCustomValidationCallback =
                (httpRequestMessage, cert, cetChain, policyErrors) =>
                {
                    return true;
                };

            HttpClient client = new HttpClient(handler);
            HttpResponseMessage response = await client.GetAsync("https://test.client.ssl/");
            //HttpResponseMessage response = await client.GetAsync("https://192.168.101.99/");
            response.EnsureSuccessStatusCode();
            string temp = await response.Content.ReadAsStringAsync();
            tb.Text = temp;
        }
```
