# php CI

## 缓存
$this->load->driver('cache', array('adapter' => 'apc', 'backup' => 'file', 'key_prefix' => 'my_'));
$this->cache->get('foo')
$this->cache->save('foo', $foo, 300);
delete clean

$this->load->driver('cache');
$this->cache->redis->save('foo', 'bar', 10);

## 表单验证
```php
class Form extends CI_Controller {
    public function index()
    {
        $this->load->helper(array('form', 'url'));
        $this->load->library('form_validation');

        $this->form_validation->set_rules('username', 'Username', 'required');
        $this->form_validation->set_rules('password', 'Password', 'required',
            array('required' => 'You must provide a %s.')
        );
        $this->form_validation->set_rules('passconf', 'Password Confirmation', 'required');
        $this->form_validation->set_rules('email', 'Email', 'required');

        if ($this->form_validation->run() == FALSE)
        {
            $this->load->view('myform');
        }
        else
        {
            $this->load->view('formsuccess');
        }
    }
}
```


## 参数获取
```php
$this->input->get('name');
$this->input->get('name');
$this->input->server('name');
$this->input->cookie('name','value',3600);
$this->input->ip_address();
$this->input->user_agent();
$this->input->request_headers();
$this->input->method(TRUE); // Outputs: POST
```

## 数据库CURD
```php
$this->db->query('YOUR QUERY HERE');

$query = $this->db->query("SELECT * FROM users;");
foreach ($query->result('User') as $user)
{
    echo $user->name; // access attributes
    echo $user->reverse_name(); // or methods defined on the 'User' class
}
```
转义查询
```php
$search = '20% raise';
$sql = "SELECT id FROM table WHERE column LIKE '%" .$this->db->escape_like_str($search)."%' ESCAPE '!'";
```

```
$this->db->insert_id()
$this->db->affected_rows()
$this->db->last_query()
$this->db->count_all('tablename');

// 返回SQL语句
$this->db->insert_string()
$this->db->update_string()
```

// 查询构造器
$query = $this->db->get('mytable');
$query = $this->db->get('mytable',10,10); // limit 10,10
$query = $this->db->get_where('mytable', array('id' => $id), $limit, $offset);

$info = $query->result();

// 聚合运算两步走
$this->db->select_avg()
$this->db->select_sum()
$this->db->select_min()
$this->db->get('tablename')

// insert
$this->db->insert('table',$data); //生成SQL并执行
$this->db->insert_batch('table',$datas); //批量插入

// update
$this->db->replace('table',$data); //=delete+insert 需要有主键

$this->db->set('field', 'field+1', FALSE);
$this->db->where('id', 2);
$this->db->update('mytable'); // gives UPDATE mytable SET field = field+1 WHERE id = 2

$this->db->set($array);

// delete
$this->db->delete('mytable', array('id' => $id));
$this->db->truncate('mytable');

// 链式操作
$query = $this->db->select('title')->where('id', $id)->limit(10, 20)->get('mytable');


// 事务处理
// CI会根据查询失败或成功，自动提交或回滚
$this->db->trans_start();
$this->db->query('AN SQL QUERY...');
$this->db->query('ANOTHER QUERY...');
$this->db->trans_complete();

// 事务异常处理
if ($this->db->trans_status() === FALSE)
{
    // generate an error... or use the log_message() function to log your error
}

// 手动执行事务
$this->db->trans_begin();

$this->db->query('AN SQL QUERY...');
$this->db->query('ANOTHER QUERY...');
$this->db->query('AND YET ANOTHER QUERY...');

if ($this->db->trans_status() === FALSE)
{
    $this->db->trans_rollback();
}
else
{
    $this->db->trans_commit();
}


