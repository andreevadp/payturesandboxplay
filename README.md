<p># payturesandboxplay<br /><strong>Payture API Testing</strong> </p>
<p dir="auto">Для своей первой самостоятельной практической работы я выбрала тестирование API сервиса Payture.&nbsp;</p>
<p dir="auto">JSON файл моей коллекции включает в себя все нижеприведенные команды.<br />Все операции выполнялись методом&nbsp;<strong>POST</strong>, передавая параметры в теле запроса.</p>
<p>Доступные команды:</p>
<ul>
<li>Команда <strong>PAY</strong> используется для выполнения платежа по одностадийной схеме.</li>
<li>Команда <strong>BLOCK</strong> выполняется в рамках двухстадийной схемы проведения платежа и замораживает деньги на карте покупателя, до завершения сделки.</li>
<li>Заблокированные средства далее могут быть списаны командой <strong>CHARGE</strong>.</li>
<li>Или разблокированы командой <strong>UNBLOCK</strong>.</li>
<li>Команда <strong>CHARGE</strong> используется для списания денежных средств с карты Покупателя, предварительно заблокированных командой <strong>BLOCK</strong>. Для ее успешного выполнения в тело запроса подставляем <strong>OrderId</strong> из ответа команды <strong>BLOCK</strong>, и переносим данные о карте и ее держателе.</li>
<li>Для полной или частичной разблокировки суммы, ранее заблокированной на карте клиента используем команду <strong>UNBLOCK</strong>, подставляя <strong>OrderId</strong>, тот же самый что блокировался в команде <strong>BLOCK</strong>.</li>
<li>Команда <strong>REFUND</strong> используется для возврата денежных средств, списанных командой <strong>PAY</strong> или <strong>CHARGE</strong>, обратно на ту же карту, с которой была совершенна транзакция.</li>
<li>Команда <strong>GETSTATE</strong> используется для получения актуального статуса платежа.</li>
<li>Команда <strong>PAY3DS</strong> предназначена для завершения списания средств с платежной карты, защищенной 3-D Secure. Выполняется после запроса <strong>PAY </strong>и получения результатов&nbsp;3‑D Secure аутентификации от банка-эмитента.</li>
<li>Команда <strong>BLOCK3DS</strong> предназначена для завершения блокирования средств на карте, защищенной 3-D Secure. Выполняется после запроса <strong>BLOCK</strong> и получения результатов&nbsp;3‑D Secure аутентификации&nbsp;от банка-эмитента.</li>
</ul>
<p><br />Заполняем требуемые параметры. Данные для <strong>PayInfo</strong> так же предоставляются сервисом Payture.<br />Для начала протестируем платеж без <strong>3DS</strong> с опциональным CVV с вводными данными:</p>
<p>номер карты - 5218851946955484,</p>
<p>код-123,</p>
<p>срок действия - 12/25.</p>
<p>Так же вводим любую сумму нашей покупки. В дальнейшем нам потребуется следить, чтобы сумма была =&lt; указанной, для успешного выполнения команд <strong>REFUND</strong>, <strong>CHARGE</strong>, что описано в документации, и при тестировании API мы убеждаемся, что ожидаемый результат соответствует фактическому, и в ответ нам придет ErrCode="AMOUNT_ERROR"<br />Все эти данные требует параметры API для совершения команды <strong>PAY</strong> и <strong>BLOCK</strong>.</p>
<p>&nbsp;</p>
<p>Так же в открытом доступе предоставлена возможность протестировать API <strong>3DS</strong>-ного платежа. Заменяем данные карты в <strong>PayInfo</strong> на ту, что требует аутефинкацию через <strong>3DS</strong>.<br />В <strong>PayInfo</strong> меняем параметры, на карту с защитой <strong>3DS 1.1</strong>:</p>
<p>номер карты - 5486732058864471,</p>
<p>код-123,</p>
<p>срок действия - 12/25.</p>
<p><br />Выполняем команду <strong>PAY</strong> и получаем большой ответ,если он в статусе Success="3DS" из него нас интересуют - OrderId={OrderId} ACSUrl="https://sandbox3-3ds.payture.com/simulp3ds/ACS/" PaReq={PaReq} ThreeDSKey={ThreeDSKey}<br />Эти параметры нам нужны для проведения аутенфикации.<br />Теперь нам нужно отправить <strong>POST</strong> запрос на прилетевший нам <strong>ACSUrl</strong> из ответа на запрос <strong>PAY</strong> или <strong>BLOCK</strong><br />Подставляем пришедший <strong>ThreeDSKey</strong> в параметр <strong>MD</strong> и <strong>PaReq</strong> в <strong>PaReq</strong> соответсвенно.<br />Получившийся запрос открываем через браузер. Теперь нам требуется получить <strong>PaRes</strong> из ответа со страницы. Сделаем мы это при помощи <strong>DevTools</strong>:</p>
<p>Открываем консоль. Нажимаем на кнопку "Получить пароль". Подставляем код, нажимаем "Подтвердить". Аутенфикация пройдена.</p>
<p>Нам пришел ответ во вкладке <strong>Network</strong>. Во вкладе <strong>Payload</strong> копируем пришедший <strong>PaRes</strong>.<br />Теперь мы можем завершить транзакцию, выполнив команду <strong>PAY3DS</strong>. Подставляем <strong>OrderID</strong> из ответа команды <strong>PAY</strong> и найденный <strong>PaRes</strong>. Наконец платеж выполнен, получаем Success="True"<br />Аналогичные действия требуются, если мы выбрали команду <strong>BLOCK3DS</strong>.</p>
<p>&nbsp;</p>
