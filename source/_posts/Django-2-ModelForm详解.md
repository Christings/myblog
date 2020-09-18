---
title: 'Django-2-ModelForm详解'
date: 2018-08-31 12:41:10
tags: Django
category: 
		- Python
		- Django
---
## 一.前言
	ModelForm：功能是将Model和Form组合起来
## 二.ModelForm
	from django.forms import ModelForm # 导入ModelForm
	from django.forms import fields
	from .models import Book
		
	class BookModelForm(forms.ModelForm):
    class Meta:
    	 # 可以在这里单独定义字段的具体类型和属性，同Form;
    	 # 如果定义的字段在model中存在，则会覆盖；
    	 name=fields.CharField(required=True)
    	 
        model = Book
        
        # fields控制在前端显示的字段
        fields = ['name', 'authors', 'publish_date']
        fields='__all__'
        
        # labels控制在前端是否显示标题
		 labels={
		 		'name': '用户名',   # 'name':'' 
             'authors': '作者',
             'publish_date': '日期',
		 }
		 
        widgets = {
            'name': forms.TextInput(attrs={'class': "form-control"}),
            'authors': forms.Select(attrs={'class': "form-control"}),
            'publish_date': forms.DateInput(attrs={'class': "form-control",
                                                   'placeholder': "YYYY-MM-DD"})
        }
        
        


 	说明：
  
		model: 对应的数据库model
		
		fields: 选择字段列表；’all’是选择所有字段
		
		exclude: 排除字段列表
		
		widgets: 插件字典
		
		labels: 前端显示字段名
		
		help_texts：帮助提示信息
		
		error_messages: 自定义错误提示
		
		localized_fields: 本地化，如：根据不同时区显示数据
## 三.View
	class Index(TemplateView):
      template_name = "index.html"
  	
      def get(self, request, *args, **kwargs):
          form = BookModelForm()    
          return self.render_to_response(context={"form": form})
  
      def post(self,request):
      		
          form = BookModelForm()
          
          # obj=model.Book.objects.get(id=1)
          #form=BookModelForm(request.POST,instance=obj)
          
         if form.is_valid():
             form.save()
             return redirect("home")
             
            	# obj = form.save(commit=False)
            	# obj.save()      # 保存单表信息
            	# obj.save_m2m()  # 保存关联多对多信息
         return self.render_to_response(context={"form": form})
	
