---
layout: post
title: Tomcat log에 대한 이해와 분리 방법
subtitle: rotatelogs를 활용한 Tomcat log 시간단위 분리
categories: infra
tags: infra was
comments: true
published: true
header-img: img/infra/linux/memory/memory.jpg
---

## 개요

---
> Tomcat의 catalina.out 로그를 시간별로 분리 하여 효율적인 로그관리를 할 수 있다. 


3일 남짓, 나름의 삽질 끝에 답을 알아낸듯 하다.






### tomcat 로그 종류

---

(1). `catalina.out` : 서버상에서 발생한 모든 내용(서버의 기동,정지,개시 등)을 기록한 파일이다. 

그렇기 때문에 생기는 로그의 양이 방대하다.

날짜별, 혹은 시간별로 로그를 분리하는것은 일반적으로 catalina.out에 사용한다.


(2). `catalina.yyyy-mm-dd.log` : 톰캣에서 생기는 로그만을 기록한다.

Standard output(표준 스트림), Standard error(표준에러)의 로깅은 제외


(3). `host-manager.log` : Tomcat Host Manager Web app 로그 (가상호스트 매니저)


(4). `manager.log` : Tomcat Manager Web App 로그 (웹콘솔)


(5). `localhost.log` : host(특정 가상호스트 대상)한정 로그





### Log Handling

---

앞선 로그의 종류를 보면 일반적으로 catalina.out에 중요 로그파일이 모두 적재되는것을 알 수 있다.

이 catalina.out이 너무 커지면 몇만, 몇십만 이상의 문장이 적재되어 로그분석이 힘들고,

경우에 따라 100기가가 넘어가면 톰캣이 중지된다는 사례도 들었다. 

따라서 보기 좋게, 혹은 안정성을 위해 날짜별로 쪼개고 그 날짜가 오래된것은 지우는 과정이 필요하다.


> 지금까지 내가 알고있고, 또는 적용해본 로그 처리 방법은 2가지이다.



#### (1) crontab을 사용하는 경우

로그를 봐야하는 사용자 혹은 개발자가 `tail -f catalina.out` 으로 실시간 확인이 필요한경우 

지난날짜 파일을 날짜라벨링을해주고 분리 후, catalina.out 파일의 내용을 비워준다. 


1. 실행파일 생성

> $ cronLog.sh 생성

=> 파일명은 예시, 해당 파일에내에 아래와같이 설정한다.


> $ cp /Tomcat 홈 경로/logs/catalina.out /Tomcat 홈 경로/logs/catalina.out.$(date +\%y-\%m-\%d).log 2>&1 

=> 지금시간의 년, 월, 일로 복사한다.


> $ cat /dev/null > /Tomcat 홈 경로/logs/catalina.out

=> 기존의 catalina.out 파일은 비워준다.


> $ find /Tomcat 홈 경로/logs -mtime +90 -name catalina\* -exec rm {} \;

=> catalina로 시작하는 파일중 생성일자가 90일 이후인 파일삭제

위와 같은 방법으로 localhost, host-manager, manager도 일정기간이 지나면 삭제되게 설정


> $ chmod 755 cronLog.sh

=> 읽기, 쓰기, 실행 권한을 부여함으로써 크론탭에서 실행 할 수 있게 설정



> $ crontab -e

> 59 23 * * * /Tomcat 홈 경로/logs/cronLog.sh

=> crontab에 위내용을 추가, 매일 23시 59분에 해당 실행파일을 동작시키겠다는 뜻





#### (2) rotatelogs를 사용하는 경우

해당방법은 하루단위로 나눠도 2~3GB에 달하는 로그를 더 잘게 쪼개기위해 사용했다.

사실 이 부분 역시 crontab을 사용해도 시간단위로 로그를 쪼갤 수 있다.

단, 해당 서버에 접근 하지 않을때의 로그까지 쪼개서 하루 24개의 로그를 만들 필요는 없다고 판단하여

Tomcat 폴더/bin/catalina.sh 파일상에서 조금의 수정을 해줬다.


> vi /Tomcat 홈 경로/bin/catalina.sh

> /touch

<div class="colorscripter-code" style="color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important; position:relative !important;overflow:auto"><table class="colorscripter-code-table" style="margin:0;padding:0;border:none;background-color:#fafafa;border-radius:4px;" cellspacing="0" cellpadding="0"><tr><td style="padding:6px;border-right:2px solid #e5e5e5"><div style="margin:0;padding:0;word-break:normal;text-align:right;color:#666;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="line-height:130%">1</div><div style="line-height:130%">2</div><div style="line-height:130%">3</div><div style="line-height:130%">4</div><div style="line-height:130%">5</div><div style="line-height:130%">6</div><div style="line-height:130%">7</div><div style="line-height:130%">8</div><div style="line-height:130%">9</div><div style="line-height:130%">10</div><div style="line-height:130%">11</div><div style="line-height:130%">12</div><div style="line-height:130%">13</div><div style="line-height:130%">14</div><div style="line-height:130%">15</div><div style="line-height:130%">16</div><div style="line-height:130%">17</div><div style="line-height:130%">18</div><div style="line-height:130%">19</div><div style="line-height:130%">20</div><div style="line-height:130%">21</div><div style="line-height:130%">22</div><div style="line-height:130%">23</div><div style="line-height:130%">24</div><div style="line-height:130%">25</div><div style="line-height:130%">26</div><div style="line-height:130%">27</div><div style="line-height:130%">28</div></div></td><td style="padding:6px 0;text-align:left"><div style="margin:0;padding:0;color:#010101;font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace !important;line-height:130%"><div style="padding:0 6px; white-space:pre; line-height:130%"><span style="color:#999999">#touch&nbsp;"$CATALINA_OUT"</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;<span style="color:#a71d5d">if</span>&nbsp;[&nbsp;<span style="color:#63a35c">"$1"</span>&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">=</span>&nbsp;<span style="color:#63a35c">"-security"</span>&nbsp;]&nbsp;;&nbsp;<span style="color:#a71d5d">then</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#a71d5d">if</span>&nbsp;[&nbsp;$have_tty&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>eq&nbsp;<span style="color:#0099cc">1</span>&nbsp;];&nbsp;<span style="color:#a71d5d">then</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#066de2">echo</span>&nbsp;<span style="color:#63a35c">"Using&nbsp;Security&nbsp;Manager"</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#a71d5d">fi</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;shift</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;eval&nbsp;$_NOHUP&nbsp;<span style="color:#63a35c">"\"$_RUNJAVA\""</span>&nbsp;<span style="color:#63a35c">"\"$LOGGING_CONFIG\""</span>&nbsp;$LOGGING_MANAGER&nbsp;$JAVA_OPTS&nbsp;$CATALINA_OPTS&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>D$ENDORSED_PROP<span style="color:#0086b3"></span><span style="color:#a71d5d">=</span><span style="color:#63a35c">"\"$JAVA_ENDORSED_DIRS\""</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>classpath&nbsp;<span style="color:#63a35c">"\"$CLASSPATH\""</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>Djava.security.manager&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>Djava.security.policy<span style="color:#0086b3"></span><span style="color:#a71d5d">=</span><span style="color:#0086b3"></span><span style="color:#a71d5d">=</span><span style="color:#63a35c">"\"$CATALINA_BASE/conf/catalina.policy\""</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>Dcatalina.base<span style="color:#0086b3"></span><span style="color:#a71d5d">=</span><span style="color:#63a35c">"\"$CATALINA_BASE\""</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>Dcatalina.home<span style="color:#0086b3"></span><span style="color:#a71d5d">=</span><span style="color:#63a35c">"\"$CATALINA_HOME\""</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>Djava.io.tmpdir<span style="color:#0086b3"></span><span style="color:#a71d5d">=</span><span style="color:#63a35c">"\"$CATALINA_TMPDIR\""</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;org.apache.catalina.startup.Bootstrap&nbsp;<span style="color:#63a35c">"$@"</span>&nbsp;start&nbsp;<span style="color:#0099cc">2</span><span style="color:#a71d5d">&gt;</span><span style="color:#0086b3"></span><span style="color:#a71d5d">&amp;</span><span style="color:#0099cc">1</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">|</span><span style="color:#0086b3"></span><span style="color:#a71d5d">/</span>usr<span style="color:#0086b3"></span><span style="color:#a71d5d">/</span>sbin<span style="color:#0086b3"></span><span style="color:#a71d5d">/</span>rotatelogs&nbsp;<span style="color:#63a35c">"$CATALINA_BASE"</span><span style="color:#0086b3"></span><span style="color:#a71d5d">/</span>logs<span style="color:#0086b3"></span><span style="color:#a71d5d">/</span>catalina.out.%Y.%m.%d.%H.log&nbsp;<span style="color:#0099cc">3600</span>&nbsp;<span style="color:#0099cc">540</span>&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">&amp;</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;<span style="color:#a71d5d">else</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;eval&nbsp;$_NOHUP&nbsp;<span style="color:#63a35c">"\"$_RUNJAVA\""</span>&nbsp;<span style="color:#63a35c">"\"$LOGGING_CONFIG\""</span>&nbsp;$LOGGING_MANAGER&nbsp;$JAVA_OPTS&nbsp;$CATALINA_OPTS&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>D$ENDORSED_PROP<span style="color:#0086b3"></span><span style="color:#a71d5d">=</span><span style="color:#63a35c">"\"$JAVA_ENDORSED_DIRS\""</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>classpath&nbsp;<span style="color:#63a35c">"\"$CLASSPATH\""</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>Dcatalina.base<span style="color:#0086b3"></span><span style="color:#a71d5d">=</span><span style="color:#63a35c">"\"$CATALINA_BASE\""</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>Dcatalina.home<span style="color:#0086b3"></span><span style="color:#a71d5d">=</span><span style="color:#63a35c">"\"$CATALINA_HOME\""</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">-</span>Djava.io.tmpdir<span style="color:#0086b3"></span><span style="color:#a71d5d">=</span><span style="color:#63a35c">"\"$CATALINA_TMPDIR\""</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;org.apache.catalina.startup.Bootstrap&nbsp;<span style="color:#63a35c">"$@"</span>&nbsp;start&nbsp;<span style="color:#0099cc">2</span><span style="color:#a71d5d">&gt;</span><span style="color:#0086b3"></span><span style="color:#a71d5d">&amp;</span><span style="color:#0099cc">1</span>&nbsp;\</div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">|</span><span style="color:#0086b3"></span><span style="color:#a71d5d">/</span>usr<span style="color:#0086b3"></span><span style="color:#a71d5d">/</span>sbin<span style="color:#0086b3"></span><span style="color:#a71d5d">/</span>rotatelogs&nbsp;<span style="color:#63a35c">"$CATALINA_BASE"</span><span style="color:#0086b3"></span><span style="color:#a71d5d">/</span>logs<span style="color:#0086b3"></span><span style="color:#a71d5d">/</span>catalina.out.%Y.%m.%d.%H.log&nbsp;<span style="color:#0099cc">3600</span>&nbsp;<span style="color:#0099cc">540</span>&nbsp;<span style="color:#0086b3"></span><span style="color:#a71d5d">&amp;</span></div><div style="padding:0 6px; white-space:pre; line-height:130%">&nbsp;</div></div><div style="text-align:right;margin-top:-13px;margin-right:5px;font-size:9px;font-style:italic"><a href="http://colorscripter.com/info#e" target="_blank" style="color:#e5e5e5text-decoration:none">Colored by Color Scripter</a></div></td><td style="vertical-align:bottom;padding:0 2px 4px 0"><a href="http://colorscripter.com/info#e" target="_blank" style="text-decoration:none;color:white"><span style="font-size:9px;word-break:normal;background-color:#e5e5e5;color:white;border-radius:10px;padding:1px">cs</span></a></td></tr></table></div>

=> 위 코드처럼 수정한다.

특히 `|/usr/sbin/rotatelogs "$CATALINA_BASE"/logs/catalina.out.%Y.%m.%d.%H.log 3600 540 &` 이부분이 중요하다.

3600은 초단위로써 1시간단위로 파일을 만들어낸다는 뜻이며

540은 국제표준시간에서 한국시간만큼을 더한 시간이다.


이와같이 설정 후 WAS를 재기동하여 서버에 접속하게되면

해당날짜의 24시간형식으로 시간별 로그가 남게된다.


### 참조글

- [`http://www.chlux.co.kr/bbs/board.php?bo_table=board02&wr_id=11`](http://www.chlux.co.kr/bbs/board.php?bo_table=board02&wr_id=11)
- [`https://cassandra.tistory.com/5`](https://cassandra.tistory.com/5)