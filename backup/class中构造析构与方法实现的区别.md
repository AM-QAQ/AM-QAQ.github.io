# 使用构造析构实现功能时
## 性能考虑
当class需要被**多次调用**时，需要频繁的创建释放其内存，这将**消耗大量的性能**。

## 内存管理
不同的class之间可能存在相互引用的关系，首先重新构造class可能导致其他变量未实现重置，其次多次重新构造class可能导致其与其他class的生命周期不同，以上问题无疑**提高了内存管理的要求**。

## 方法实现
将所需的功能定义为方法后可以通过对应的方法重置内容实现多次调用，使class的生命周期与主函数相同，这样便省去了多次创建释放的麻烦

## 附代码
```C++
class Atlas
{
public:
	Atlas() = default;
	~Atlas() = default;

	void load_from_file(LPCTSTR path_template, int num)
	{
		img_list.clear();		//为img_list重新分配大小，避免多次调用中出现大小不匹配的情况
		img_list.resize(num);

		TCHAR path_file[256];
		for (int i = 0; i < num; ++i)
		{
			_stprintf_s(path_file, path_template, i + 1);
			loadimage(&img_list[i], path_file);
		}
	}

	void clear()	//清空图片集中已加载的图片对象
	{
		img_list.clear();
	}

	int get_size()
	{
		return (int)img_list.size();
	}

	IMAGE* get_image(int idx)
	{
		if (idx < 0 || idx >= img_list.size())
			return nullptr;
		return &img_list[idx];
	}

	void add_image(const IMAGE& img)
	{
		img_list.push_back(img);
	}

private:
	vector<IMAGE> img_list;
};
```
