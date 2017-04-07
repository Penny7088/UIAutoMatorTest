# UIAutoMatorTestimport 

java.io.BufferedReader;

import java.io.File;

import java.io.FileInputStream;

import java.io.FileOutputStream;

import java.io.IOException;

import java.io.InputStream;

import java.io.InputStreamReader;

import java.io.OutputStreamWriter;

public class UIAutomatorTools { 

private static String WORKSPACE_PATH = ""; 

public static void main(String args[]) {  
    
    new UIAutomatorTools("Demo", "Main.WechatAutoTest", "WechatAutoChat",    "1"); 
    
    } 
    
public UIAutomatorTools(String jarName, String testPackageclass,   String testFunction, String androidId) {  
    
    super();  
    
    System.out.println("######################");  
    
    System.out.println(" --AutoTest Start--");  
    
    System.out.println("######################");  
    
    WORKSPACE_PATH = getWorkSpace();  
    
    System.out.println("自动测试项目工作空间:\t\n" + WORKSPACE_PATH);  
    
    /******** 启动测试 *******/  
    
    // 1--创建Build.xml文件  
    
    createBuildXml(jarName, androidId);  
    
    // 2--修改Build.xml文件  
    
    modfileFile("buld.xml", new FileFilter() {   
    
    public String onFilte(String line) {    
    
    // TODO Auto-generated method stub    
    
    if (line.matches(".*help.*")) {    
    
          return line.replaceAll("help", "build");    
          
          }    
          
          return line;   }  }); 
          
          antBuild();  
            
        pushJarToAndroid(WORKSPACE_PATH + "\\bin\\" + jarName + ".jar");  
        
        if (testFunction.equals("")) {   
        
          runTest(jarName, testPackageclass);  
          
          } else {   
          
          runTest(jarName, testPackageclass + "#" + testFunction);  
          
          } 
          
          } 
          
private void runTest(String jarName, String testName) {  

  System.out.println(" --测试开始--");  
  
  execCmd("adb shell uiautomator runtest " + jarName + ".jar --nohup -c "    + testName);  
  
  System.out.println(" --测试结束--"); 
  
  } 
  
private void antBuild() {  

  System.out.println(" --2--编译build.xml开始--");  
  
  execCmd("cmd /c ant -buildfile " + "\"" + WORKSPACE_PATH + "\"");   
  
  System.out.println(" --2--编译build.xml结束--"); 
  
  } 
  
private void pushJarToAndroid(String localPath) {  

  System.out.println(" --push jar开始--");  

  execCmd("adb push " + "\"" + localPath + "\"" + " /data/local/tmp/");  
  
  System.out.println(" --push jar结束--"); 
  
} 

private String getWorkSpace() {  

  File director = new File("");  
  
  return director.getAbsolutePath(); 
  
} 

public void createBuildXml(String jarName, String androidId) {  

  System.out.println(" --创建build.xml开始--");  
  
  execCmd("cmd /c android create uitest-project -n " + jarName + " -t "    + androidId + " -p " + "\"" + WORKSPACE_PATH + "\"");  
  
  System.out.println(" --创建build.xml完成--"); 
  
} 

public void modfileFile(String fileName, FileFilter FileFilter) {  

  System.out.println(" --修改" + fileName + "开始--");  
  
  StringBuffer stringBuffer = new StringBuffer();  
  
  try {   
  
  File file = new File(fileName);   
  
  if (file.isFile() && file.exists()) {    
  
    InputStreamReader read = new InputStreamReader(new FileInputStream(file));    
    
    BufferedReader bufferedReader = new BufferedReader(read);    
    
    String lineText;    
    
    while ((lineText = bufferedReader.readLine()) != null) {     
    
    stringBuffer = stringBuffer.append(FileFilter.onFilte(lineText)).append("\t\n");    
    
  }   
  
  read.close();   

} else {    

  System.out.println("找不到" + fileName + "文件");   

}  

} catch (Exception e) {   
  
  System.out.println("读取" + fileName + "出错");   
  
  e.printStackTrace();  
  
}  

rewriteFile(fileName, new String(stringBuffer));  

System.out.println(" --修改" + fileName + "完成--"); 

} 

private interface FileFilter {  

  String onFilte(String line); 

} 

public void rewriteFile(String path, String content) {  
  
  File file = new File(path);  
  
  if (!file.exists()) {   
  
  try {    
  
  file.createNewFile();   
  
  } catch (IOException e) {    
  
  // TODO Auto-generated catch block    
  
  e.printStackTrace();   
  
  }  
  
  }  
  
  try {   
    
    OutputStreamWriter bwl = new OutputStreamWriter(new FileOutputStream(file));   
    
    bwl.write(content);   
    
    bwl.close();  
  
  } catch (IOException e) {   
  
    e.printStackTrace();  
    
    } 
  } 
  
public void execCmd(String cmd) {  

  System.out.println("ExecCmd:" + cmd);  
  
  try {   
  
    Process p = Runtime.getRuntime().exec(cmd);   
    
    InputStream input = p.getInputStream();   
    
    BufferedReader reader = new BufferedReader(new InputStreamReader(input, "GBK"));   
    
    String line;   
    
    while ((line = reader.readLine()) != null) {   
      
      System.out.println(line);   
      }   
      
      InputStream errorInput = p.getErrorStream();   
      
      BufferedReader errorReader = new BufferedReader(new InputStreamReader(errorInput, "GBK"));   
      
      String eline;   
      
      while ((eline = errorReader.readLine()) != null) {    
      
        System.out.println(eline);   
        
      }  
      
    } catch (IOException e) {   
    
      e.printStackTrace();  } 
      
      }
      
      }
