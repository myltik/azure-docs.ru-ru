
## <a name="update-app"></a>Вызов настраиваемого интерфейса API из приложения iOS

Чтобы вызвать настраиваемый интерфейс API из клиента iOS, используйте метод `MSClient invokeAPI`. Существует две версии этого метода: одна для JSON-форматированных запросов, а другая — для любого типа данных:

	/// Invokes a user-defined API of the Mobile Service.  The HTTP request and
	/// response content will be treated as JSON.
	-(void)invokeAPI:(NSString *)APIName
	            body:(id)body
	      HTTPMethod:(NSString *)method
	      parameters:(NSDictionary *)parameters
	         headers:(NSDictionary *)headers
	      completion:(MSAPIBlock)completion;

	/// Invokes a user-defined API of the Mobile Service.  The HTTP request and
	/// response content can be of any media type.
	-(void)invokeAPI:(NSString *)APIName
	            data:(NSData *)data
	      HTTPMethod:(NSString *)method
	      parameters:(NSDictionary *)parameters
	         headers:(NSDictionary *)headers
	      completion:(MSAPIDataBlock)completion;


Например, чтобы отправить JSON-запрос в настраиваемый интерфейс API с именем sendEmail, передайте `NSDictionary` для параметров запроса:

	NSDictionary *emailHeader = @{ @"to": @"email.com", @"subject" : @"value" };

	[self.client invokeAPI:@"sendEmail"
	     body:emailBody
	     HTTPMethod:@"POST"
	     parameters:emailHeader
	     headers:nil
	     completion:completion ];
		

<!---HONumber=August15_HO6-->