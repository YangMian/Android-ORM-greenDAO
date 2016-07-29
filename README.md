# Android-ORM-greenDAO
##前言
我相信，在平时的开发过程中，大家一定会或多或少地接触到 SQLite。然而在使用它时，我们往往需要做许多额外的工作，像编写 SQL 语句与解析查询结果等。所以，适用于 Android 的ORM 框架也就孕育而生了，现在市面上主流的框架有 OrmLite、SugarORM、Active Android、Realm 与 GreenDAO。
##为什么是greenDAO?
1.GreenDao 数据库开发库 性能非常高效.

2.GreenDao是一个 把sqlite底层封装起来的快速 开发库,开发者使用起来像操作集合那样简单.
![](https://github.com/YangMian/Android-ORM-greenDAO/blob/master/raw/1.png) 

##greenDAO 性能远远高于同类的 ORMLite的原因:
1.不是反射 ormlite.

2.基于代码引擎生成代码(代码生成器Code generation).
![](https://github.com/YangMian/Android-ORM-greenDAO/blob/master/raw/2.png)
##开始使用
####一. 在 ANDROID 工程中配置「GREENDAO GENERATOR」模块
1.在 .src/main 目录下新建一个与 java 同层级的「java-gen」目录，用于存放由 greenDAO 生成的 Bean、DAO、DaoMaster、DaoSession 等类。
![](https://github.com/YangMian/Android-ORM-greenDAO/blob/master/raw/3.png)
![](https://github.com/YangMian/Android-ORM-greenDAO/blob/master/raw/4.png)

2.配置 Android 工程（app）的 build.gradle，如图分别添加 sourceSets 与dependencies。 
![](https://github.com/YangMian/Android-ORM-greenDAO/blob/master/raw/5.png)
####二. 新建「GREENDAO GENERATOR」模块 (纯 JAVA 工程）
1.通过 File -> New -> New Module -> Java Library -> 填写相应的包名与类名 -> Finish.
![](https://github.com/YangMian/Android-ORM-greenDAO/blob/master/raw/6.png)
![](https://github.com/YangMian/Android-ORM-greenDAO/blob/master/raw/7.png)
![](https://github.com/YangMian/Android-ORM-greenDAO/blob/master/raw/8.png)

2.配置 daoexamplegenerator 工程的 build.gradle，添加 dependencies.
![](https://github.com/YangMian/Android-ORM-greenDAO/blob/master/raw/9.png)


注意： 我们的 Java 工程只有一个类，它的内容决定了「GreenDao Generator」的输出，你可以在这个类中通过对象、关系等创建数据库结构，下面我将以注释的形式详细讲解代码内容。

'''

    public static void main(String[] args) throws IOException, Exception {
		// TODO Auto-generated method stub

		// ①　创建 代码生成工程
		// ②　添加jar
		// ③　描述实体Entiry
		// ④　把实体放到Scheme
		// Schema allEntities=new Schema(版本, 自动生成代码的包路径);
		Schema allEntities = new Schema(1, "com.lym.dao");
		// allEntities.addEntity(类名)
		Entity person = allEntities.addEntity("Person");
		person.addIdProperty().primaryKey().autoincrement();// id int 自增长
		person.addStringProperty("name");
		person.addIntProperty("age");
		// ⑤　生成代码
		//  最后我们将使用 DAOGenerator 类的 generateAll() 方法自动生成代码,第二个参数是生成代码的路径
		new DaoGenerator().generateAll(allEntities, "..GreenDaoDemo/src");
	}

'''
####最后编写测试类代码————数据库的增删改查

    public class TestPersonDao extends AndroidTestCase {

	@Test
	public void testInsert() {
		
		DaoSession session = getSession();
		//获取Person的增删改查对象
		PersonDao dao=session.getPersonDao();
		Person p=new Person();
		p.setAge(14);
		p.setName("王方");
		dao.insert(p);
		
	}
	@Test
	public void testQuery() {
		DaoSession session = getSession();
		//获取Person的增删改查对象
		PersonDao dao=session.getPersonDao();
		List<Person> ps=dao.loadAll();
		for(Person p:ps)
		{
			System.out.println(p.getName());
		}
	}
	@Test
	public void testUpdate() {
		DaoSession session = getSession();
		//获取Person的增删改查对象
		PersonDao dao=session.getPersonDao();
		Person p=dao.load(1L);
		System.out.println(p.getAge());
		p.setAge(15);
		dao.update(p);
	}
	@Test
	public void testDelete() {
		DaoSession session = getSession();
		//获取Person的增删改查对象
		PersonDao dao=session.getPersonDao();
		dao.deleteByKey(1L);
	}

	private DaoSession getSession() {
		//指定 数据   im.db
		//sqltieOpenhlepr -->DevOpenHelper
		SQLiteDatabase db=new DaoMaster.DevOpenHelper(mContext, "im.db", null).getWritableDatabase();

		//获取管理者
		DaoMaster master=new DaoMaster(db);
		//获取Dao的集合
		DaoSession session=master.newSession();
		return session;
    	}

    }
  
  感谢tangqi92（ https://github.com/tangqi92 ） Android studio中使用greenDao的图片



