# bpmonline-first-tutorial1.	
В разделе Юр лица в карточке клиента реализовать действие «Получить полное наименование Юр лица» , при нажатие на которое будет выводиться модально окно: «Название Юр Лица = <Наименование>»
- Написать сервис на с#.
- Привязать событие onCLick на вызов сервиса, обработать результат вызова сервиса и отобразить в модально окне

Решение:
1.	Начнем с написания сервиса на C#. Для этого нужно перейти в главное меню > дизайнер системы > управление конфигурацией > вкладка конфигурация > Выбираем актуальный рабочий пакет > во вкладке Схемы нажимаем кнопку Добавить - Исходный код > Сохранить > Публиковать
Открываем Visual Studio Code. Ищем файл которую создали через bpm’online
Пишем сервис на C#
namespace Terrasoft.Configuration
{
    using System;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Data;
    using System.Globalization;
    using System.Linq;
    using System.Runtime.Serialization;
    using System.ServiceModel;
    using System.ServiceModel.Activation;
    using System.ServiceModel.Web;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Web;
    using Terrasoft.Common;
    using Terrasoft.Core;
    using Terrasoft.Core.DB;
    using Terrasoft.Core.Entities;
    using Terrasoft.Core.Factories;
    using Terrasoft.Core.Packages;
    using Terrasoft.Web;
    using global::Common.Logging;

    //using Terrasoft.Core;
    // Класс сервиса помечен обязательными атрибутами [ServiceContract] и
    // [AspNetCompatibilityRequirements] с параметрами.
    [ServiceContract]
    [
        AspNetCompatibilityRequirements(
            RequirementsMode = AspNetCompatibilityRequirementsMode.Required)
    ]
    public class CustomConfigurationServiceTest
    {
        private UserConnection _userConnection;

        // Метод сервиса помечен обязательными атрибутами [OperationContract] и
        // [WebInvoke] с параметрами.
        [OperationContract]
        [
            WebInvoke(
                Method = "POST",
                RequestFormat = WebMessageFormat.Json,
                BodyStyle = WebMessageBodyStyle.Wrapped,
                ResponseFormat = WebMessageFormat.Json)
        ]
        public string GetTransformValue(string inputParam)
        {
            var result = "Название Юр Лица : ";
            try
            {
                _userConnection =
                    (UserConnection)
                    HttpContext.Current.Session["UserConnection"];
                using (
                    DBExecutor dbExecutor = _userConnection.EnsureDBConnection()
                )
                {
                    var qp = new QueryParameterCollection();
                    qp
                        .Add(new QueryParameter {
                            Name = "TsBIN",
                            Value = inputParam
                        });
                    var sql =
                        "SELECT \"Name\" " +
                        "FROM \"Account\" " +
                        "WHERE \"TsBIN\" = :TsBIN";
                    using (var rd = dbExecutor.ExecuteReader(sql, qp))
                    {
                        while (rd.Read())
                        {
                            result += rd.GetString(0);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
                result += ex.Message;
            }
            return result;
        }
 
    }
}





Открываем дизайнер исходного кода. Копируем код. Сохранение. Публикация.


2.	Обработать событие onClick. Для начала нужно найти исходный код Js. Через F12 открываем и переходим во вкладку Source. Как можно заметить мы попадаем в исходный код js файла. Где описаны все скрипты и событий. Переходим на Visual Studio Code и ищем js файл с таким названием и содержанием. 
3.	actionMenuItems.addItem(this.getButtonMenuItem({
4.	                            "Caption": "getCompanyName",
5.	                            "Tag": "getCompanyName"
6.	                        }));
7.	                        
8.	                        return actionMenuItems;
9.	                    },
10.	                    getCompanyName: function() {
11.	                        //this.showInformationDialog("Название Юр Лица : "+this.get("Name"));
12.	                        var parameters = {
13.	                            inputParam: this.get("TsBIN")
14.	                        };
15.	                        ServiceHelper.callService(
16.	                            "CustomConfigurationServiceTest",
17.	                            "GetTransformValue",
18.	                            function(response) {
19.	                                if (response) {
20.	                                    var result = response.GetTransformValueResult;
21.	                                    if (result) {
22.	                                        this.showInformationDialog(result);
23.	                                        return;
24.	                                    } else {
25.	                                        var resultObject = {success: true};
26.	                                        callback.call(scope, resultObject);
27.	                                    }
28.	                                }
29.	                            }, parameters, this);
30.	                    },


Создаем событие, сохраним (публикация js произойдет автоматический)

