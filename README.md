# login_facebook_ionic

1) Criar em https://developers.facebook.com/ um aplicativo

2) criar aplicacao ionic: ionic start nome-app tabs

3) instalar cordova no projeto: npm install -g cordova
3.1) gerar hash do windows: keytool -exportcert -alias androiddebugkey -keystore "C:\Users\USERNAME\.android\debug.keystore" | "PATH_TO_OPENSSL_LIBRARY\bin\openssl" sha1 -binary | "PATH_TO_OPENSSL_LIBRARY\bin\openssl" base64
3.2) na pasta do projeto executar:  keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
3.2.1) pegar o SHA1 que está em hexadecimal e converter para BASE64.

4) Instalação do plugin:
4.1) ionic cordova plugin add cordova-plugin-facebook4 --variable APP_ID="2342342432342342342" --variable APP_NAME="app-tutorial"
4.2) npm install --save @ionic-native/facebook

5) verificar se no arquivo config.xml está parecido com esse exemplo abaixo:
  <plugin name="cordova-plugin-facebook4" spec="^1.9.1">
      <variable name="APP_ID" value="2342342432342342342" />
      <variable name="APP_NAME" value="app-tutorial" />
  </plugin>
  
6) em: platforms > android > app > src > main > res >  values > string.xml adicionar:
    <string name="fb_app_id">2342342432342342342</string>
    <string name="fb_app_name">app-tutorial</string>


import { Component } from '@angular/core';
import { IonicPage, NavController, NavParams } from 'ionic-angular';
import { Facebook } from '@ionic-native/facebook';
import { Usuario } from '../../model/usuario.model';
import { AlertController } from "ionic-angular";

@IonicPage()
@Component({
  selector: 'page-autenticacao',
  templateUrl: 'autenticacao.html',
})
export class AutenticacaoPage {

  constructor(public navCtrl: NavController, public navParams: NavParams,public facebook: Facebook,public alertController: AlertController) {
  }


  loginFacebook(){
    let permissions = new Array<string>();
    permissions = ["public_profile", "email"];

    this.facebook.login(permissions).then((response) => {
      let params = new Array<string>();

      this.facebook.api("/me?fields=name,email", params)
      .then(res => {

        
          //estou usando o model para criar os usuarios
          let usuario = new Usuario();
          usuario.nome = res.name;
          usuario.email = res.email;
          usuario.senha = res.id;
          usuario.login = res.email;
        
          console.log('email: ',res.email,' :nome: ',res.name,':name:',res.senha,':id:',res.id);


      }, (error) => {
        alert(error);
        console.log('ERRO LOGIN: ',error);
        this.alertPadrao("Error 1 ","Error 2 ","AKIIIIIIII");
      })
    }, (error) => {
      alert(error);
    });
  }

  alertPadrao(status: string, error: string, mensagem: string){
    let alert = this.alertController.create({
        title: 'Erro '+status+':'+error,
        message: mensagem,
        enableBackdropDismiss: false,
        buttons: [{text:'Ok'}]
    });
    alert.present(); //apresenta o alert
} 


}
