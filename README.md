# MineSweeper
import java.applet.Applet;
import java.applet.AudioClip;
import java.awt.* ;
import java.awt.event.* ;
import java.awt.geom.* ;
import java.io.File;
import java.io.IOException;
import java.net.MalformedURLException;
import java.net.URL;

import javax.imageio.ImageIO;
import javax.sound.midi.InvalidMidiDataException;
import javax.sound.midi.MidiSystem;
import javax.sound.midi.MidiUnavailableException;
import javax.sound.midi.Sequencer;
import javax.swing.* ;
import javax.swing.event.* ;
import javax.swing.plaf.ColorUIResource;

import java.awt.image.BufferedImage;
public class MineSweeperJSongPeriod3 extends JFrame implements ActionListener,MouseListener {
	int rows = 10 ;
	int cols = 10 ;
	int numMines = 10 ;
	GridLayout layout = new GridLayout ( rows , cols ) ;
   
	boolean [ ] mines = new boolean [ rows * cols ] ;
	boolean [ ] clickable = new boolean [ rows * cols ] ;
	boolean lost = false ;
	boolean won = false ;
	int [ ] numbers = new int [ rows * cols ] ;
	JButton [ ] buttons = new JButton [ rows * cols ] ;
	boolean [ ] clickdone = new boolean [ rows * cols ] ;
	JMenuItem newGameButton = new JMenuItem ( "New game" ) ;
	JMenuItem difficulty = new JMenuItem ( "Options") ;
	JLabel mineLabel;
	JPanel p = new JPanel ( ) ;
	JEditorPane helpContent;
	JScrollPane helpPane;
	JEditorPane helpContent2;
	JScrollPane helpPane2;
	JMenuItem exitItem;
	JMenuItem mineItem;
	JMenuItem playItem;
	JMenuItem aboutItem;
	JMenu selectMenu;
	JMenu optionMenu;
	JMenu editMenu;
	JMenuBar menuBar;
	JTextField m;;	     
	BufferedImage bi;
	AudioClip coinSound;
	File midiFile;
	Sequencer sequencer;
	File midiFile2;
	Sequencer sequencer2;
	File midiFile3;
	Sequencer sequencer3;
	File midiFile4;
	Sequencer sequencer4;
	public MineSweeperJSongPeriod3 ( ) {
		p.setLayout ( layout ) ;
	    
		try {
			coinSound = Applet.newAudioClip(new URL("file:smw_coin.wav"));
		}
		catch (MalformedURLException e) {
			System.out.println(e.getMessage());
		}
		midiFile = new File("gameOver2.midi");
		try {
			sequencer = MidiSystem.getSequencer();
			sequencer.setSequence(MidiSystem.getSequence(midiFile));
			sequencer.open();
			sequencer.setLoopCount(Sequencer.LOOP_CONTINUOUSLY);
		}
		catch(MidiUnavailableException mue) {
			System.out.println("Midi device unavailable!" + mue.getMessage()); }
		catch(InvalidMidiDataException imde) {
			System.out.println("Invalid Midi data!" + imde.getMessage()); }
		catch(IOException ioe) {
			System.out.println("I/O Error!" + ioe.getMessage()); }
		midiFile2 = new File("stageClear.midi");
		try {
			sequencer2 = MidiSystem.getSequencer();
			sequencer2.setSequence(MidiSystem.getSequence(midiFile2));
			sequencer2.open();
			sequencer2.setLoopCount(1);
		}
		catch(MidiUnavailableException mue) {
			System.out.println("Midi device unavailable!" + mue.getMessage()); }
		catch(InvalidMidiDataException imde) {
			System.out.println("Invalid Midi data!" + imde.getMessage()); }
		catch(IOException ioe) {
			System.out.println("I/O Error!" + ioe.getMessage()); }
		midiFile3 = new File("Fight.midi");
		try {
			sequencer3 = MidiSystem.getSequencer();
			sequencer3.setSequence(MidiSystem.getSequence(midiFile3));
			sequencer3.open();
			sequencer3.setLoopCount(1);
		}
		catch(MidiUnavailableException mue) {
			System.out.println("Midi device unavailable!" + mue.getMessage()); }
		catch(InvalidMidiDataException imde) {
			System.out.println("Invalid Midi data!" + imde.getMessage()); }
		catch(IOException ioe) {
			System.out.println("I/O Error!" + ioe.getMessage()); }
		
		midiFile4 = new File("Pause.midi");
		try {
			sequencer4 = MidiSystem.getSequencer();
			sequencer4.setSequence(MidiSystem.getSequence(midiFile4));
			sequencer4.open();
			sequencer4.setLoopCount(1);
		}
		catch(MidiUnavailableException mue) {
			System.out.println("Midi device unavailable!" + mue.getMessage()); }
		catch(InvalidMidiDataException imde) {
			System.out.println("Invalid Midi data!" + imde.getMessage()); }
		catch(IOException ioe) {
			System.out.println("I/O Error!" + ioe.getMessage()); }
		setupI ( ) ;
		for ( int i = 0 ; i < ( rows * cols ) ; i ++ ) {
			p.add ( buttons [ i ] ) ;
		}
		ImageIcon start = new ImageIcon("sanji.png");
		//buttons[0].add(start);
		menuBar = new JMenuBar();
        selectMenu = new JMenu("Game");
        optionMenu = new JMenu("Option");
        editMenu = new JMenu("Help");
        exitItem = new JMenuItem("Exit");
        aboutItem = new JMenuItem("About");
        playItem = new JMenuItem("HOW TO PLAY");
        mineItem = new JMenuItem("Total Mine");
        exitItem.addActionListener(new exitListener());
        aboutItem.addActionListener(new AboutListener());
        playItem.addActionListener(new HowtoListener());
        mineItem.addActionListener(new OptionsListener());
		newGameButton.addActionListener ( this ) ;
		selectMenu.add ( newGameButton ) ;
		selectMenu.add(difficulty);
		selectMenu.add(newGameButton);
		difficulty.addActionListener ( this ) ;
        selectMenu.add(exitItem);
        optionMenu.add(mineItem);
        editMenu.add(aboutItem);
        editMenu.add(playItem);
        menuBar.add(selectMenu);
        menuBar.add(optionMenu);
        menuBar.add(editMenu);
        try {
			helpContent = new JEditorPane(new URL("file:HOW TO PLAY.html"));
		} catch (MalformedURLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
        helpPane = new JScrollPane(helpContent);
        try {
			helpContent2 = new JEditorPane(new URL("file:about.html"));
		} catch (MalformedURLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
        helpPane2 = new JScrollPane(helpContent);
        mineLabel = new JLabel ( "Mines: " + numMines + " Marked: 0" ) ;
        try {
        	bi = ImageIO.read(
        	new URL("http://fc07.deviantart.net/fs71/f/2014/025/d/9/one_piece_icon_by_dviini-d73n1xt.png"));
        	setPreferredSize(
        	new Dimension(bi.getWidth(),bi.getHeight()));
        	} catch (IOException ioe) {
        	}
		this.setJMenuBar ( menuBar ) ;
		this.add ( p ) ;
		this.add ( mineLabel , BorderLayout.SOUTH ) ;
		this.pack ( ) ;
		this.setVisible ( true ) ;
	}
//	public void ImagePanel() {
//	       try {                
//	          image = ImageIO.read(new File("sanji.png"));
//	       } catch (IOException ex) {
//	            // handle exception...
//	       }
//	       Graphics g = image.getGraphics();
//	       g.drawImage(image,0, 0,1000,1000,null);
//
//	}	
	public void fillMines ( ) {
		int needed = numMines ;
		while ( needed > 0 ) {
			int x = ( int ) Math.floor ( Math.random ( ) * rows ) ;
			int y = ( int ) Math.floor ( Math.random ( ) * cols ) ;
			if ( ! mines [ ( rows * y ) + x ] ) {
				mines [ ( rows * y ) + x ] = true ;
				needed -- ;
			}
		}
	}
	public void fillNumbers ( ) {
		for ( int x = 0 ; x < rows ; x ++ ) {
			for ( int y = 0 ; y < cols ; y ++ ) {
				int cur = ( rows * y ) + x ;
				if ( mines [ cur ] ) {
					numbers [ cur ] = 0 ;
					continue ;
				}
				int temp = 0 ;
				boolean l = ( x - 1 ) >= 0 ;
				boolean r = ( x + 1 ) < rows ;
				boolean u = ( y - 1 ) >= 0 ;
				boolean d = ( y + 1 ) < cols ;
				int left = ( rows * ( y ) ) + ( x - 1 ) ;
				int right = ( rows * ( y ) ) + ( x + 1 ) ;
				int up = ( rows * ( y - 1 ) ) + ( x ) ;
				int upleft = ( rows * ( y - 1 ) ) + ( x - 1 ) ;
				int upright = ( rows * ( y - 1 ) ) + ( x + 1 ) ;
				int down = ( rows * ( y + 1 ) ) + ( x ) ;
				int downleft = ( rows * ( y + 1 ) ) + ( x - 1 ) ;
				int downright = ( rows * ( y + 1 ) ) + ( x + 1 ) ;
				if ( u ) {
					if ( mines [ up ] ) {
						temp ++ ;
					}
					if ( l ) {
						if ( mines [ upleft ] ) {
							temp ++ ;
						}
					}
					if ( r ) {
						if ( mines [ upright ] ) {
							temp ++ ;
						}
					}
				}
				if ( d ) {
					if ( mines [ down ] ) {
						temp ++ ;
					}
					if ( l ) {
						if ( mines [ downleft ] ) {
							temp ++ ;
						}
					}
					if ( r ) {
						if ( mines [ downright ] ) {
							temp ++ ;
						}
					}
				}
				if ( l ) {
					if ( mines [ left ] ) {
						temp ++ ;
					}
				}
				if ( r ) {
					if ( mines [ right ] ) {
						temp ++ ;
					}
				}
				numbers [ cur ] = temp ;
			}
		}
	}
	public void setupI ( ) {
		for ( int x = 0 ; x < rows ; x ++ ) {
			for ( int y = 0 ; y < cols ; y ++ ) {
				mines [ ( rows * y ) + x ] = false ;
				clickdone [ ( rows * y ) + x ] = false ;
				clickable [ ( rows * y ) + x ] = true ;
				buttons [ ( rows * y ) + x ] = new JButton ( /*"" + ( x * y )*/) ;
				buttons [ ( rows * y ) + x ].setPreferredSize ( new Dimension (
						45 , 45 ) ) ;
				buttons [ ( rows * y ) + x ].addActionListener ( this ) ;
				buttons [ ( rows * y ) + x ].addMouseListener ( this ) ;
				
				
			}
		}
		fillMines ( ) ;
		fillNumbers ( ) ;
	}
	public void setupI2 ( ) {
		this.remove ( p ) ;
		p = new JPanel ( ) ;
		layout = new GridLayout ( rows , cols ) ;
		p.setLayout ( layout ) ;
		buttons = new JButton [ rows * cols ] ;
		mines = new boolean [ rows * cols ] ;
		clickdone = new boolean [ rows * cols ] ;
		clickable = new boolean [ rows * cols ] ;
		numbers = new int [ rows * cols ] ;
		setupI ( ) ;
		for ( int i = 0 ; i < ( rows * cols ) ; i ++ ) {
			p.add ( buttons [ i ] ) ;
		}
		this.add ( p ) ;
		this.pack ( ) ;
		fillMines ( ) ;
		fillNumbers ( ) ;
	}
	public void setup ( ) {
		for ( int x = 0 ; x < rows ; x ++ ) {
			for ( int y = 0 ; y < cols ; y ++ ) {
				mines [ ( rows * y ) + x ] = false ;
				clickdone [ ( rows * y ) + x ] = false ;
				clickable [ ( rows * y ) + x ] = true ;
				buttons [ ( rows * y ) + x ].setEnabled ( true ) ;
				buttons [ ( rows * y ) + x ].setText ( "" ) ;
			}
		}
		fillMines ( ) ;
		fillNumbers ( ) ;
		lost = false ;
		mineLabel.setText ( "mines: " + numMines + " marked: 0" ) ;
	}
	public static void main ( String [ ] args ) {
		new MineSweeperJSongPeriod3 ( ) ;
	}
	public void paintComponent(Graphics g) {
		g.drawImage(bi,0,0,null);
   }
	public void actionPerformed ( ActionEvent e ) {
		if ( e.getSource ( ) == difficulty ) {	
			if (!sequencer3.isRunning()){
				sequencer3.start();
				
				System.out.println("Playing MIDI File");
			}else if (sequencer3.isRunning()) {
				sequencer3.stop();
				
				System.out.println("Pausing MIDI File");
			}
			rows = Integer.parseInt ( ( String ) JOptionPane.showInputDialog (					
					this , "Rows" , "Rows" , JOptionPane.PLAIN_MESSAGE , null ,
					null , 10 ) ) ;
			cols = Integer.parseInt ( ( String ) JOptionPane.showInputDialog (
					this , "Columns" , "Columns" , JOptionPane.PLAIN_MESSAGE ,
					null , null , 10 ) ) ;
			numMines = Integer.parseInt ( ( String ) JOptionPane
					.showInputDialog ( this , "Mines" , "Mines" ,
							JOptionPane.PLAIN_MESSAGE , null , null , 10 ) ) ;
			setupI2 ( ) ;
		}
		if ( ! won ) {
			for ( int x = 0 ; x < rows ; x ++ ) {
				for ( int y = 0 ; y < cols ; y ++ ) {
					if ( e.getSource ( ) == buttons [ ( rows * y ) + x ]
							&& ! won && clickable [ ( rows * y ) + x ] ) {
						doCheck ( x , y ) ;
						break ;
					}
				}
			}
		}
		if ( e.getSource ( ) == newGameButton ) {
			if (!sequencer4.isRunning()){
				sequencer4.start();
				
				System.out.println("Playing MIDI File");
			}
			else if (sequencer4.isRunning()) {
				sequencer4.stop();
				
				System.out.println("Pausing MIDI File");
			}
			setup ( ) ;
			won = false ;
			return ;
		}
		checkWin ( ) ;
	}
	public void mouseEntered ( MouseEvent e ) {
	}
	public void mouseExited ( MouseEvent e ) {
	}
	public void mousePressed ( MouseEvent e ) {
		
	}
	public void mouseReleased ( MouseEvent e ) {
	}
	public void mouseClicked ( MouseEvent e ) {
		if(e.getButton()==1){
			coinSound.play();
		}
		if ( e.getButton ( ) == 3 ) {
			int n = 0 ;
			for ( int x = 0 ; x < rows ; x ++ ) {
				for ( int y = 0 ; y < cols ; y ++ ) {
					if ( e.getSource ( ) == buttons [ ( rows * y ) + x ] ) {
						clickable [ ( rows * y ) + x ] = ! clickable [ ( rows * y )
								+ x ];					
					}
					if ( ! clickdone [ ( rows * y ) + x ] ) {
						if ( ! clickable [ ( rows * y ) + x ] ) {
							buttons [ ( rows * y ) + x ].setText ( "X" ) ;
							n ++ ;
						} else {
							buttons [ ( rows * y ) + x ].setText ( "" ) ;
						}
						mineLabel.setText ( "mines: " + numMines + " marked: "
								+ n ) ;
					}
				}
			}
		}
	}
	public void doCheck ( int x , int y ) {
		int cur = ( rows * y ) + x ;
		boolean l = ( x - 1 ) >= 0 ;
		boolean r = ( x + 1 ) < rows ;
		boolean u = ( y - 1 ) >= 0 ;
		boolean d = ( y + 1 ) < cols ;
		int left = ( rows * ( y ) ) + ( x - 1 ) ;
		int right = ( rows * ( y ) ) + ( x + 1 ) ;
		int up = ( rows * ( y - 1 ) ) + ( x ) ;
		int upleft = ( rows * ( y - 1 ) ) + ( x - 1 ) ;
		int upright = ( rows * ( y - 1 ) ) + ( x + 1 ) ;
		int down = ( rows * ( y + 1 ) ) + ( x ) ;
		int downleft = ( rows * ( y + 1 ) ) + ( x - 1 ) ;
		int downright = ( rows * ( y + 1 ) ) + ( x + 1 ) ;
		clickdone [ cur ] = true ;
		buttons [ cur ].setEnabled ( false ) ;
		if ( numbers [ cur ] == 0 && ! mines [ cur ] && ! lost && ! won ) {
			if ( u && ! won ) {
				if ( ! clickdone [ up ] && ! mines [ up ] ) {
					clickdone [ up ] = true ;
					buttons [ up ].doClick ( ) ;
				}
				if ( l && ! won ) {
					if ( ! clickdone [ upleft ] && numbers [ upleft ] != 0
							&& ! mines [ upleft ] ) {
						clickdone [ upleft ] = true ;
						buttons [ upleft ].doClick ( ) ;
					}
				}
				if ( r && ! won ) {
					if ( ! clickdone [ upright ] && numbers [ upright ] != 0
							&& ! mines [ upright ] ) {
						clickdone [ upright ] = true ;
						buttons [ upright ].doClick ( ) ;
					}
				}
			}
			if ( d && ! won ) {
				if ( ! clickdone [ down ] && ! mines [ down ] ) {
					clickdone [ down ] = true ;
					buttons [ down ].doClick ( ) ;
				}
				if ( l && ! won ) {
					if ( ! clickdone [ downleft ] && numbers [ downleft ] != 0
							&& ! mines [ downleft ] ) {
						clickdone [ downleft ] = true ;
						buttons [ downleft ].doClick ( ) ;
					}
				}
				if ( r && ! won ) {
					if ( ! clickdone [ downright ]
							&& numbers [ downright ] != 0
							&& ! mines [ downright ] ) {
						clickdone [ downright ] = true ;
						buttons [ downright ].doClick ( ) ;
					}
				}
			}
			if ( l && ! won ) {
				if ( ! clickdone [ left ] && ! mines [ left ] ) {
					clickdone [ left ] = true ;
					buttons [ left ].doClick ( ) ;
				}
			}
			if ( r && ! won ) {
				if ( ! clickdone [ right ] && ! mines [ right ] ) {
					clickdone [ right ] = true ;
					buttons [ right ].doClick ( ) ;
				}
			}
		} else {
			buttons [ cur ].setText ( "" + numbers [ cur ] ) ;
			if ( ! mines [ cur ] && numbers [ cur ] == 0 ) {
				buttons [ cur ].setText ( "" ) ;
			}
		}
		if ( mines [ cur ] && ! won ) {
			buttons [ cur ].setText ( "0" ) ;
			doLose ( ) ;
		}
	}
	public void checkWin ( ) {
		for ( int x = 0 ; x < rows ; x ++ ) {
			for ( int y = 0 ; y < cols ; y ++ ) {
				int cur = ( rows * y ) + x ;
				if ( ! clickdone [ cur ] ) {
					if ( mines [ cur ] ) {
						continue ;
					} else {
						return ;
					}
				}
			}
		}
		doWin ( ) ;
	}
	public void doWin ( ) {
		if ( ! lost && ! won ) {
			won = true ;
			if (!sequencer2.isRunning()){
				sequencer2.start();
				
				System.out.println("Playing MIDI File");
			}else if (sequencer2.isRunning()) {
				sequencer2.stop();
				
				System.out.println("Pausing MIDI File");
			}
			JOptionPane.showMessageDialog ( null ,
					"you win!\nstarting a new game" , "you lose" ,
					JOptionPane.INFORMATION_MESSAGE ) ;
			
			newGameButton.doClick ( ) ;
			
		}
	}
	public void doLose ( ) {
		if ( ! lost && ! won ) {
			lost = true ;
			for ( int i = 0 ; i < rows * cols ; i ++ ) {
				if ( ! clickdone [ i ] ) {
					buttons [ i ].doClick ( 0 ) ;
				}
			}
			new UIManager();
			if (!sequencer.isRunning()){
				sequencer.start();
				
				System.out.println("Playing MIDI File");
			}else if (sequencer.isRunning()) {
				sequencer.stop();
				
				System.out.println("Pausing MIDI File");
			}
			UIManager.put("OptionPane.background",new ColorUIResource(255,2,40));
			JOptionPane.showMessageDialog ( null ,
					"you lose!\nstarting a new game" , "you lose" ,
					JOptionPane.ERROR_MESSAGE ) ;
			
			setup ( ) ;
		}
	}
	private class HowtoListener implements ActionListener {

	    public void actionPerformed(ActionEvent arg0) {
	    	JOptionPane.showMessageDialog(null, helpContent, "How To Play", JOptionPane.PLAIN_MESSAGE, null);
	              
	    }
	  }

	  private class AboutListener implements ActionListener {

	    public void actionPerformed(ActionEvent arg0) {
	    	JOptionPane.showMessageDialog(null, helpContent2, "About ", JOptionPane.PLAIN_MESSAGE, null);
	    }
	  }
	  private class exitListener implements ActionListener {

		    public void actionPerformed(ActionEvent e) {
		    	if(e.getActionCommand().equals("Exit")){
		    		System.exit(0);
		    		}
		    }
	  }
	  private class OptionsListener implements ActionListener {

		    public void actionPerformed(ActionEvent arg0) {
		    	 String input;
		    	JOptionPane minePane = new JOptionPane();
		    	input =JOptionPane.showInputDialog("Total numbers of Mine");
		    	numMines = Integer.parseInt(input);
		    }
		    
	  }
}
