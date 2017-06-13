### Java Program to Check a Server Status and Generate an Alarm if The Server is Not Available

* java -jar ServerStatus.jar http://192.168.241.12/server-status.php D:\\alarm.wav

```java

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.URL;
import java.net.URLConnection;
import java.util.Timer;
import java.util.TimerTask;
import java.util.logging.Level;
import java.util.logging.Logger;

class ServerStatusCheck {

    String serverIP;
    String mediaFile;

    public ServerStatusCheck(String serverIP, String mediaFile) throws Exception {
        URL url = new URL(serverIP);
        URLConnection yc = url.openConnection();
        BufferedReader in = new BufferedReader(new InputStreamReader(yc.getInputStream()));
        String inputLine;
        String serverStatus = "404";
        while ((inputLine = in.readLine()) != null) {
            serverStatus = inputLine;
        }

        in.close();

        if ("200".equals(serverStatus)) {
            System.out.println(serverIP + " Server is OK");
        } else {
            System.out.println(serverIP + " Server is NOT OK");
            new ProcessBuilder("C:\\Program Files\\Windows Media Player\\wmplayer.exe", mediaFile).start();
        }
    }
}

public class ServerStatus {

    public static void main(String[] args) throws Exception {
        Timer timer = new Timer();
        timer.scheduleAtFixedRate(new TimerTask() {
            @Override
            public void run() {
                try {
                    new ServerStatusCheck(args[0], args[1]);
                } catch (Exception ex) {
                    Logger.getLogger(ServerStatus.class.getName()).log(Level.SEVERE, null, ex);
                }
            }
        }, 0, 60000);
    }
}
```
