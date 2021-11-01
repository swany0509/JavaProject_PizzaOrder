
import java.awt.*;
import java.awt.event.*;
import javax.swing.*;

public class PizzaOrderManual extends JFrame {
//1. 서쪽패널 전역변수
	// 토핑 관련
	String toppings[] = { "버섯", "파인애플", "치즈", "올리브", "베이컨" };
	ImageIcon[] toppingimg = new ImageIcon[5]; // 토핑의 이미지들
	JCheckBox toppingbtn[] = new JCheckBox[5]; // 토핑을 선택하기 위한 체크박스
	int tps[] = { 0, 0, 0, 0, 0 }; // 선택되었는지 확인하기 위한 변수
	// 사이즈 관련
	String sizes[] = { "스몰 ", "미디움 ", "라지 " };
	int sizeprice[] = { 2000, 5000, 10000 }; // 사이즈 가격
	ButtonGroup gp = new ButtonGroup();
	JRadioButton sizebtn[] = new JRadioButton[3]; // size를 선택하기 위한 라디오 버튼
//2. 가운데 패널 전역 변수 (피자 선택)
	String pizzas[] = { "치즈 피자", "고구마 피자", "베이컨 피자", "불고기 피자", "야채 피자", "콤비네이션 피자", "페퍼로니 피자", "포테이토 피자", "핫치킨 피자",
			"바베큐 피자", "쉬림프 피자" };
	int pizzaprice[] = { 5000, 8000, 8000, 8000, 7000, 8000, 8000, 8000, 10000, 10000, 10000 }; // 피자 가격
	
	JRadioButton[] pizza_select = new JRadioButton[11]; // 피자를 선택하기 위한 버튼
//3. 동쪽 패널 전역변수(사이드메뉴)
	String[] sidemenu = { "사이다 500ml", "사이다  1.5L", "콜라 500ml", "콜라  1.5L", "환타 500ml", "환타 1.5L", "감자튀김", "피클",
			"핫소스" };
	int[] sideprice = { 1000, 2000, 1000, 2000, 1000, 2000, 5000, 500, 100 }; // 사이드 메뉴 가격
	JTextField sideselect[] = new JTextField[9];
	// 현재 선택중인 사이드메뉴 관련
	JTextArea sidelist = new JTextArea(9, 30); // 현재 선택 사이드메뉴 목록
	int sidesum = 0;
//4. 남쪽 패널 전역변수
	// 현재 선택중인 피자
	String top = ""; // 추가된 토핑들 표시
	String pizzasize = ""; // 고른 피자 사이즈 표시
	String pizzatype = ""; // 피자 종류 표시
	int sum = 0;
	JTextField selecting = new JTextField(100); // 선택 결과가 표시될 라벨
	// 주문내역
	JTextArea orders = new JTextArea(8, 10);
	// 결제 예정 금액
	int finalprice = 0;
	JLabel value = new JLabel("      " + finalprice);

	PizzaOrderManual() {
		setTitle("Pizza Order");
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		Container c = getContentPane();

		c.setLayout(new BorderLayout()); //기본 레이아웃
		
		//각 구역을 JPanel형 메소드를 통해 삽입한다.
		c.add(Northpanel(), BorderLayout.NORTH);
		c.add(Westpanel(), BorderLayout.WEST);
		c.add(Centerpanel(), BorderLayout.CENTER);
		c.add(Eastpanel(), BorderLayout.EAST);
		c.add(Southpanel(), BorderLayout.SOUTH);

		selecting.setFocusable(false);
		pack();
		setVisible(true);
	}

	public JPanel Northpanel() {
		//주문 방법에 대한 설명이 들어간다.
		JPanel panel = new JPanel();
		panel.setBackground(Color.white);
		panel.setBorder(BorderFactory.createTitledBorder("주문방법"));
		panel.setLayout(new BoxLayout(panel, BoxLayout.Y_AXIS));
		panel.add(new JLabel(" 1. 피자와 사이즈, 토핑을 선택 후 '피자추가' 버튼을 누르세요."));
		panel.add(new JLabel(" 2. 추가주문을 통해 음료와 사이드메뉴를 선택하세요."));
		panel.add(new JLabel(" 3. 주문내역과 금액을 확인하고, 주문완료 버튼을 누르면 새 창으로 주문내역이 출력됩니다."));
		return panel;
	}

	public JPanel Westpanel() {
		//토핑 과 사이즈를 선택한다.
		JPanel panel = new JPanel();
		panel.setBackground(Color.orange);
		//기본 레이아웃
		panel.setLayout(new BoxLayout(panel, BoxLayout.Y_AXIS));
		Box topping = Box.createVerticalBox();
		Box size = Box.createVerticalBox();
		topping.setBorder(BorderFactory.createTitledBorder("토핑 추가"));
		size.setBorder(BorderFactory.createTitledBorder("사이즈"));
		String[] space = {"         ", "", "         ", "     ","     "};
		//토핑 버튼 삽입
		for (int i = 0; i < toppings.length; i++) {
			toppingimg[i] = new ImageIcon("" + toppings[i] + ".png");
			toppingbtn[i] = new JCheckBox(toppings[i] + "(+1000)" + space[i], toppingimg[i]);
			toppingbtn[i].setBorderPainted(true);
			toppingbtn[i].addItemListener(new MyitemListener());
			toppingbtn[i].setBackground(Color.white);
			topping.add(toppingbtn[i]);
		}

		//사이즈 버튼 삽입
		for (int i = 0; i < sizes.length; i++) {
			sizebtn[i] = new JRadioButton(sizes[i] + "(+" + String.valueOf(sizeprice[i]) + ")");
			sizebtn[i].addItemListener(new MyitemListener());
			sizebtn[i].setBackground(Color.white);
			gp.add(sizebtn[i]);
			size.add(sizebtn[i]);
		}
		sizebtn[0].setSelected(true); //기본 사이즈 선택

		panel.add(topping);
		panel.add(size);
		return panel;
	}

	public JPanel Centerpanel() {
		//피자를 선택한다.
		JPanel panel = new JPanel();
		panel.setBackground(Color.white);
		panel.setBorder(BorderFactory.createTitledBorder("피자 선택"));
		panel.setLayout(new GridLayout(4, 4, 2, 2)); //기본 레이아웃
		ImageIcon pizzaimg[] = { new ImageIcon("cheese.jpg"), new ImageIcon("sweetpotato.jpg"), new ImageIcon("bacon.jpg"),
				new ImageIcon("bulgogi.jpg"), new ImageIcon("vegetable.jpg"), new ImageIcon("combination.jpg"),
				new ImageIcon("pepperoni.jpg"), new ImageIcon("potato.jpg"), new ImageIcon("hotchicken.jpg"),
				new ImageIcon("barbeque.jpg"), new ImageIcon("shrimp.jpg") };
		ButtonGroup pizzagroup = new ButtonGroup();
		
		//JRadioButton을 이용해 피자 버튼을 삽입한다.
		ButtonGroup group = new ButtonGroup();
		for (int i = 0; i < pizza_select.length; i++) {
			pizza_select[i] = new JRadioButton(pizzas[i] + "(+" + pizzaprice[i] + ")", pizzaimg[i]);
			pizza_select[i].addItemListener(new MyitemListener());
			group.add(pizza_select[i]);
			panel.add(pizza_select[i]);
			pizza_select[i].setBorderPainted(true);
			pizza_select[i].setBackground(Color.white);
		}
		pizza_select[0].setSelected(true);
		
		//피자 선택완료 버튼을 누르면 주문내역에 현재 선택중인 피자가 추가되고 피자 선택이 초기화된다.
		JButton order = new JButton("피자선택완료");
		order.setFont(new Font("bold", Font.BOLD, 35));
		order.addActionListener(new MyactionListener());
		panel.add(order);
		return panel;
	}

	public JPanel Eastpanel() {
		//사이드메뉴를 선택한다.
		JPanel panel = new JPanel();
		JPanel drink = new JPanel();
		drink.setBackground(Color.pink);
		JPanel side = new JPanel();
		side.setBackground(Color.pink);

		panel.setLayout(new BoxLayout(panel, BoxLayout.Y_AXIS)); //기본 레이아웃

		drink.setBorder(BorderFactory.createTitledBorder("음료"));
		drink.setLayout(new GridLayout(6, 2));
		side.setBorder(BorderFactory.createTitledBorder("사이드메뉴"));
		side.setLayout(new GridLayout(3, 2));
		sidelist.setBorder(BorderFactory.createTitledBorder("현재 추가된 메뉴"));
		sidelist.setFocusable(false);
		
		//JLabel과 JTextField를 이용해 사이드메뉴 선택 컴포넌트를 삽입한다.
		for (int i = 0; i < 9; i++) {
			sideselect[i] = new JTextField(15);
			sideselect[i].addKeyListener(new MykeyListener());
			if (i < 6) {
				drink.add(new JLabel(sidemenu[i] + "  " + sideprice[i]));
				drink.add(sideselect[i]);
			} else {
				side.add(new JLabel(sidemenu[i] + "  " + sideprice[i]));
				side.add(sideselect[i]);
			}
		}

		panel.add(drink);
		panel.add(side);
		panel.add(sidelist); //현재 선택된 사이드메뉴 목록

		return panel;
	}

	public JPanel Southpanel() {
		//주문을 마무리한다.
		JPanel panel = new JPanel();
		panel.setLayout(new FlowLayout());//기본 레이아웃
		panel.setBackground(Color.lightGray);

		Box box1 = Box.createVerticalBox();
		Box label = Box.createHorizontalBox();
		//현재 선택중인 피자를 보여준다.
		label.setBorder(BorderFactory.createTitledBorder("현재 선택중인 피자"));
		label.add(selecting);
		box1.add(label);
		//현재 주문목록을 보여준다.
		JScrollPane scroll = new JScrollPane(orders);
		scroll.setBorder(BorderFactory.createTitledBorder("주문 내역"));
		orders.setFocusable(false);
		box1.add(scroll);
		panel.add(box1);

		JPanel subpanel = new JPanel();
		subpanel.setLayout(new GridLayout(3, 1));
		//결제 예정금액을 보여준다.
		subpanel.add(value);
		value.setFont(new Font("궁서", Font.BOLD, 40));
		value.setBorder(BorderFactory.createTitledBorder("결제 예정 금액"));
		//주문내용을 초기화한다.
		JButton reset = new JButton("  초기화  ");
		reset.setFont(new Font("bold", Font.BOLD, 20));
		reset.addActionListener(new MyactionListener2());
		subpanel.add(reset);
		//주문을 완료한다.
		JButton finish = new JButton("주문 완료");
		finish.setFont(new Font("bold", Font.BOLD, 20));
		finish.addActionListener(new MyactionListener4());
		subpanel.add(finish);
		panel.add(subpanel);

		return panel;
	}

	class MyitemListener implements ItemListener {
		public void itemStateChanged(ItemEvent e) {
			Component k = (Component) e.getItem();
			top = ""; // 실행될 때 초기화 해줌 피자종류랑 사이즈라벨은 라디오버튼이라 if문안에서 초기화해야함
			// 토핑은 독립적이라 시작할 떄 초기화해줘야함
			if (e.getStateChange() == ItemEvent.SELECTED) { // 선택되면 sum에 더하고 아니면 sum에서 뺌
				k.setBackground(Color.yellow);
				for (int i = 0; i < toppings.length; i++) // 토핑 계산
					if (k == toppingbtn[i]) {
						tps[i] = 1; // 선택되면 tps[i]를 1로 바꿔서 선택됨을 표시
						sum += 1000;
					}
				for (int i = 0; i < sizes.length; i++) // 사이즈 계산
					if (k == sizebtn[i]) {
						pizzasize = "";
						sum += sizeprice[i];
						pizzasize = sizes[i];
					}
				for (int i = 0; i < pizzas.length; i++) // 피자 종류 계산
					if (k == pizza_select[i]) {
						pizzatype = "";
						sum += pizzaprice[i];
						pizzatype = pizzas[i];
					}
			} else {
				k.setBackground(Color.white);
				for (int i = 0; i < toppings.length; i++) // 토핑 계산
					if (k == toppingbtn[i]) {
						tps[i] = 0;
						sum -= 1000;
					}
				for (int i = 0; i < sizes.length; i++) // 사이즈 계산
					if (k == sizebtn[i])
						sum -= sizeprice[i];
				for (int i = 0; i < pizzas.length; i++) // 피자 종류 계산
					if (k == pizza_select[i])
						sum -= pizzaprice[i];
			}
			for (int i = 0; i < toppings.length; i++)
				if (tps[i] == 1)
					top += toppings[i] + " "; // tps[i]가 1로 바뀐것 -> 선택된 것 , 즉 선택된 것들만 라벨에 더함
			if (tps[0] == 0 && tps[1] == 0 && tps[2] == 0 && tps[3] == 0 && tps[4] == 0)
				top = "없음 ";
			selecting.setText(pizzasize + pizzatype + ", 토핑 : " + top + ">>" + sum + "원");
			// 각각의 string 값들은 주문 버튼을 눌렀을 때 다른 패널로 전달하기 위해서 전역변수로 선언함
		}
	}
	//피자 선택을 완료했을때 버튼을 누르면 내역을 추가하고 선택을 초기화한다.
	class MyactionListener implements ActionListener {
		public void actionPerformed(ActionEvent e) {
			String total = pizzasize + pizzatype + ", 토핑 : " + top + ">>" + sum + "원"; //현재 선택 내용을 가져온다.
			orders.append(selecting.getText() + "\n"); //내용을 내역에 추가
			finalprice += sum; //결제금액 증가
			value.setText(String.valueOf(finalprice));
			for (int i = 0; i < toppings.length; i++) { //선택 초기화
				toppingbtn[i].setSelected(false);
				tps[i] = 0;
			}
			sizebtn[0].setSelected(true);
			pizza_select[0].setSelected(true);
		}
	}
	//주문내역을 초기화한다.
	class MyactionListener2 implements ActionListener {
		public void actionPerformed(ActionEvent e) {
			orders.setText("");
			sidesum = 0;
			finalprice = 0;
			value.setText("      " + finalprice);
			sidelist.setText("");
			for (int i = 0; i < toppings.length; i++) {
				toppingbtn[i].setSelected(false);
				tps[i] = 0;
			}
			for (int i = 0; i < sideselect.length; i++) {
				sideselect[i].setText("");
			}
			sizebtn[0].setSelected(true);
			pizza_select[0].setSelected(true);
		}
	}
	//사이드메뉴의 수량을 키보드로 입력했을때 현재 선택 사이드메뉴와 결제예정금액을 갱신한다.
	class MykeyListener extends KeyAdapter {
		public void ketPressed(KeyEvent e) {
			sidesum=0;
		}
		
		public void keyReleased(KeyEvent e) {

			finalprice -= sidesum; //사이드메뉴 총액을 먼저 뺀다
			sidesum = 0; //0으로 다시 설정
			int nn; //반복 사용을 피하기 위한 임시 변수
			JTextField t = (JTextField) e.getSource();
			StringBuffer text = new StringBuffer(""); // sidelist의 내용을 갱신하기위해 임시 버퍼 설정
			for (int i = 0; i < 9; i++) {
				String temp = sideselect[i].getText();
				if (isnumber(temp)) { //isnumber(String s) : 값이 숫자인지 아닌지 판별
					nn = sideprice[i] * Integer.valueOf(temp);
					text.append(sidemenu[i] + " X " + temp + ">>" + nn + "원\n"); //입력값이 숫자면 내역 추가
					sidesum += nn;
				}
			}
			finalprice += sidesum; //결제 금액에 다시 더함
			if (finalprice == 0)
				value.setText("      " + finalprice); //컴포넌트 길이를 맞추기 위함
			else
				value.setText("" + finalprice);
			sidelist.setText(new String(text));
		}
	}
	//주문을 완료했을 때 새 창을 띄운다.
	class MyactionListener4 implements ActionListener {
		public void actionPerformed(ActionEvent e) {
			new tmp(); //새 창을 띄움
		}
	}
	//새 창으로 주문목록을 보여준다.
	public class tmp extends JFrame {
		tmp() {
			setTitle("주문내역");
			JTextArea t = new JTextArea();
			t.setText(orders.getText() + "\n" + sidelist.getText() + "\n\n 총 결제금액 : " + finalprice + "원");
			add(t);
			setSize(500, 500);
			setVisible(true);
		}
	}
	//스트링 값이 숫자인지 아닌지 판별하는 메소드. 사이드메뉴 수량 입력시 문자를 입력해버리는 오류를 방지하기 위함.
	public boolean isnumber(String input) {
		try {
			Integer.parseInt(input);
			return true;
		} catch (NumberFormatException e) {
			return false;
		}
	}

	public static void main(String[] args) {
		new PizzaOrderManual();
	}
}