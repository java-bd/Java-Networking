### Java Program to Check a Server Status and Generate an Alarm if The Server is Not Available

* #### java -jar ServerStatus.jar D:\alarm.wav

```java
/*
ServerList.txt
http://192.168.241.12/server-status.php
http://192.168.241.246/server-status.php
 */

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.InputStreamReader;
import java.net.URL;
import java.net.URLConnection;
import java.util.LinkedList;
import java.util.Timer;
import java.util.TimerTask;
import java.util.logging.Level;
import java.util.logging.Logger;

class ServerStatusCheck {

    String serverIP;
    String mediaFile;

    public ServerStatusCheck(LinkedList<String> serverList, String mediaFile) throws Exception {
        for (String serverIP : serverList) {
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
}

public class ServerStatus {

    public static void main(String[] args) throws Exception {

        LinkedList<String> serverList = new LinkedList<>();
        String mediaFile = args[0];

        try (BufferedReader br = new BufferedReader(new FileReader("ServerList.txt"))) {
            String line;
            while ((line = br.readLine()) != null) {
                serverList.add(line);
            }
        }

        Timer timer = new Timer();
        timer.scheduleAtFixedRate(new TimerTask() {
            @Override
            public void run() {
                try {
                    new ServerStatusCheck(serverList, mediaFile);
                } catch (Exception ex) {
                    Logger.getLogger(ServerStatus.class.getName()).log(Level.SEVERE, null, ex);
                }
            }
        }, 0, 5000);
    }
}

```
