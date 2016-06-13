# servlet-tutorial


## Requirements
- Liferay IDE
- Apache Tomcat *

## Outline
1. Setting Web Server Tomcat (jika belum)
2. Membuat DAO (Data Access Object)
3. Membuat Servlet
4. Deploy ke Tomcat



DAO.java

'''
package com.web.filecounter;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;

public class DAO {

  public int getCount() {
    int count = 0;
    // Load the file with the counter
    FileReader fileReader = null;
    BufferedReader bufferedReader = null;
    PrintWriter writer = null;
    try {
      File f = new File("FileCounter.initial");
      if (!f.exists()) {
        f.createNewFile();
        writer = new PrintWriter(new FileWriter(f));
        writer.println(0);
      }
      if (writer != null) {
        writer.close();
      }

      fileReader = new FileReader(f);
      bufferedReader = new BufferedReader(fileReader);
      String initial = bufferedReader.readLine();
      count = Integer.parseInt(initial);
    } catch (Exception ex) {
      if (writer != null) {
        writer.close();
      }
    }
    if (bufferedReader != null) {
      try {
        bufferedReader.close();
      } catch (IOException e) {
        e.printStackTrace();
      }
    }
    return count;
  }

  public void save(int count) throws Exception {
    FileWriter fileWriter = null;
    PrintWriter printWriter = null;
    fileWriter = new FileWriter("FileCounter.initial");
    printWriter = new PrintWriter(fileWriter);
    printWriter.println(count);

    // make sure to close the file
    if (printWriter != null) {
      printWriter.close();
    }
  }
} 
'''

FileCounter.java

'''
package com.web.filecounter;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

/**
 * Servlet implementation class FileCounter
 */
@WebServlet("/FileCounter")
public class FileCounter extends HttpServlet {
	private static final long serialVersionUID = 1L;

	int count;
	private DAO dao;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public FileCounter() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
	    HttpSession session = request.getSession(true);
	    // Set the session valid for 5 secs
//	    session.setMaxInactiveInterval(5);
	    response.setContentType("text/plain");
	    PrintWriter out = response.getWriter();
	    if (session.isNew()) {
	      count++;
	    }
	    out.println("This site has been accessed " + count + " times.");
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
	}
	  @Override
  public void init() throws ServletException {
    dao = new DAO();
    try {
      count = dao.getCount();
    } catch (Exception e) {
      getServletContext().log("An exception occurred in FileCounter", e);
      throw new ServletException("An exception occurred in FileCounter"
          + e.getMessage());
    }
  }
  
  public void destroy() {
    super.destroy();
    try {
      dao.save(count);
    } catch (Exception e) {
      e.printStackTrace();
    }
  }
}
'''
