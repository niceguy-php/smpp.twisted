SMPP 3.4 client built on Twisted


pip install twisted==19.2.0rc2

pip install enum

pip install pyOpenSSL==17.0.0

pip install smpp.pdu

pip install smpp.twisted

pip install mock    #unittest使用

pip uninstall enum34


注意：

运行环境python2.7以上

安装pyOpenSSL时会自动安装enum34和enum冲突，需要pip uninstall enum34


1.运行smpp协议的SMSC模拟器

方法一：python demo_smsc_simulator.py

方法二：解压SMPPSimulator.zip,找到startup.bat，windows运行，端口：5510，可登录:http://localhost:8081,账号：forest_luo,密码:root

<authenticate name="whoami" role="authorized">

		<version>0x30</version>

		<system_type>logica</system_type>

		<account>333</account>

		<password>0555</password>

</authenticate>


2.运行smpp客户端

python demo_client.py


3.运行单元测试

cd ./smpp.twisted/smpp/twisted/tests/
python -m unittest test_smpp_server.SMPPServerTestCase.testTRXSubmitSM


参考项目：

https://hub.docker.com/r/jookies/jasmin

https://github.com/jookies/jasmin

SMPP PDU parsing based on smpp.pdu: https://github.com/mozes/smpp.pdu

SMPP 3.4 Client based on smpp.twisted: https://github.com/mozes/smpp.twisted

GSMP 3.38 based on Python messaging: https://github.com/pmarti/python-messaging



Example
-------
    import logging
    from twisted.internet import reactor, defer
    from smpp.twisted.client import SMPPClientTransceiver, SMPPClientService
    from smpp.twisted.config import SMPPClientConfig

    class SMPP(object):

        def __init__(self, config=None):
            if config is None:
                config = SMPPClientConfig(host='localhost', port=999, username='uname', password='pwd')
            self.config = config
        
        @defer.inlineCallbacks
        def run(self):
            try:
                #Bind
                smpp = yield SMPPClientTransceiver(self.config, self.handleMsg).connectAndBind()
                #Wait for disconnect
                yield smpp.getDisconnectedDeferred()
            except Exception, e:
                print "ERROR: %s" % str(e)
            finally:
                reactor.stop()
    
        def handleMsg(self, smpp, pdu):
            """
            NOTE: you can return a Deferred here
            """
            print "Received pdu %s" % pdu
    
    if __name__ == '__main__':
        logging.basicConfig(level=logging.DEBUG)
        SMPP().run()
        reactor.run()
        
Credits
=======

http://www.nowsms.com/discus/messages/1/24856.html

* Thanks to [rtrdev](https://github.com/rtrdev) for adding support for SMPP servers
* Thanks to [Fourat Zouari](https://github.com/fourat) for finding and fixing an enquirelinks bug
