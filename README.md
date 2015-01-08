# Send-Email-By-java
用java发送邮件的例子程序
import java.util.Properties;

import javax.mail.Authenticator;
import javax.mail.Message;
import javax.mail.Message.RecipientType;
import javax.mail.PasswordAuthentication;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;


import com.framework.util.DateTimeUtil;
import com.myt.model.MailInfo;
import com.ram.model.User;


/******************************************************************************
 * @ClassName:    发送邮件的工具类   
 * @Author:       [qiaoxg]   
 * @CreateDate:   [2014-11-19 上午10:29:05]   
 * @Version:      [v1.0] 
 */

public class EmailUtils {

	public static void sendHtmlMail(MailInfo info)throws Exception{
		Message message = getMessage(info);
		message.setContent(info.getContent(), "text/html;charset=utf-8");
		Transport.send(message);
	}
	
	public static void sendTextMail(MailInfo info)throws Exception{
		Message message = getMessage(info);
		message.setText(info.getContent());
		Transport.send(message);
		System.out.println("success！");
	}
	
	private static Message getMessage(MailInfo info) throws Exception{
		final Properties  p = System.getProperties() ;
		p.setProperty("mail.smtp.host", info.getHost());
		p.setProperty("mail.smtp.auth", "true");
		p.setProperty("mail.smtp.user", info.getFormName());
		p.setProperty("mail.smtp.pass", info.getFormPassword());
		
		Session session = Session.getInstance(p, new Authenticator(){
			protected PasswordAuthentication getPasswordAuthentication() {
				return new PasswordAuthentication(p.getProperty("mail.smtp.user"),p.getProperty("mail.smtp.pass"));
			}
		});
		session.setDebug(true);
		Message message = new MimeMessage(session);
		message.setSubject(info.getSubject());
		message.setReplyTo(InternetAddress.parse(info.getReplayAddress()));
		message.setFrom(new InternetAddress(p.getProperty("mail.smtp.user"),"Web Manager"));
		message.setRecipient(RecipientType.TO,new InternetAddress(info.getToAddress()));
		
		return message ;
	}
	
	/**
	 * 注册成功后,向用户发送帐户激活链接的邮件
	 * 
	 * @param user未激活的用户
	 */
	public static void sendAccountActivateEmail(User user) throws Exception{
		MailInfo info = new MailInfo();
		info.setHost("smtp.163.com");
		info.setFormName("");//发送邮件的邮箱地址
		info.setFormPassword("");//发送邮箱的密码
		info.setReplayAddress("");
		info.setToAddress(user.getUserEmail());//用户的注册邮箱
		info.setSubject("口袋澳洲用户激活邮件");//邮件的主题
		info.setContent(
				"<div class='email' style='border:1px solid #ccc;margin:0 auto; width:600px; font-size:12px; padding:20px;'>"
				+ "<div>亲爱的用户<span style='font-weight: bold;'>"
				+ user.getUserName()
				+ "</span>: 您好</div></br>"
				+ "<div style=' margin-left:20px;'>恭喜您注册成功，点击下面链接即可激活帐号。</div>"
				+ "<div style=' margin-left:20px;'>----------------------------------------------------------------------</div>"
				+ "<div style=' margin-left:20px;'><p>帐号注册时间："+user.getRegistDateTime()+"</p>"
				+ "<p>帐号注册用户："+user.getUserName()+"</p>"
				+ "<p>帐号注册邮箱："+user.getUserEmail()+"</p>"
				+ "</div>"
				+ "<div style=' margin-left:20px;'>----------------------------------------------------------------------</div>"
				+ "<div style=' margin-left:20px;'><a href='"
				+ GenerateLinkUtils.generateActivateLink(user)
				+ "' style='color:#FF0000;'>"
				+ GenerateLinkUtils.generateActivateLink(user)
				+ "</a></div></br>"
				+ "<div style=' margin-left:20px;'>(如果无法点击该URL链接地址，请将它复制并粘帖到浏览器的地址输入框，然后单击回车即可) </div></br>"
				+ "</div>"
		);//邮件的内容
		sendHtmlMail(info);
	}
}
