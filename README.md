


MainActivity

package com.music.laoawangmusic;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;

import com.music.laoawangmusic.activity.RegisterActivity;
import com.music.laoawangmusic.activity.man.ManManageActivity;
import com.music.laoawangmusic.activity.user.UserManageActivity;
import com.music.laoawangmusic.dao.UserDao;
import com.music.laoawangmusic.until.DBUntil;
import com.music.laoawangmusic.until.Tools;

public class MainActivity extends AppCompatActivity {
    private static final int REQUEST_CODE_READ_EXTERNAL_STORAGE = 1001;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);

        // 初始化数据库
        DBUntil db = new DBUntil(this); // 创建一个数据库链接

        EditText accountT = findViewById(R.id.login_account);
        EditText pwdT = findViewById(R.id.login_pwd);
        Button denglu = findViewById(R.id.login_denglu_btn);
        Button register = findViewById(R.id.login_denglu_register);

        // 执行登录
        denglu.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String account = accountT.getText().toString();
                String pwd = pwdT.getText().toString();
                if (!validateInput(account, pwd)) {
                    return;
                }
                Tools.addPrePreferencesData(MainActivity.this, "account", account);
                int sta = UserDao.isLogin(account, pwd);
                if (sta == -1) {
                    Tools.Toast(MainActivity.this, "账号密码输入错误");
                } else if (sta == 0) {
                    Tools.Toast(MainActivity.this, "登录用户");
                    Intent intent = new Intent(MainActivity.this, UserManageActivity.class);
                    startActivity(intent);
                } else if (sta == 1) {
                    Tools.Toast(MainActivity.this, "登录管理员");
                    Intent intent = new Intent(MainActivity.this, ManManageActivity.class);
                    startActivity(intent);
                } else {
                    Tools.Toast(MainActivity.this, "数据格式错误。");
                }
            }
        });

        // 注册按钮点击事件
        register.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // 打开注册界面
                Intent intent = new Intent(MainActivity.this, RegisterActivity.class);
                startActivity(intent);
            }
        });
    }

    // 新增方法：验证输入
    private boolean validateInput(String account, String pwd) {
        if (account == null || account.equals("")) {
            Tools.Toast(MainActivity.this, "请输入账号");
            return false;
        } else if (pwd == null || pwd.equals("")) {
            Tools.Toast(MainActivity.this, "请输入密码");
            return false;
        }
        return true;
    }
}
