# Useful Gems

## Kaminari
Kaminari is a Scope & Engine based, clean, powerful, agnostic, customizable and sophisticated paginator for Rails 3+
pagination을 쉽게 해주는 gem
https://github.com/amatsuda/kaminari

**controller**
20개당 나누었을 때 `params[:page]`번째 page를 보여준다
```ruby
@items = @items.page(params[:page]).per(20)
```

**view**
```rhtml
<%= paginate @items %>
```

전체 갯수를 셀 때 `total_count`를 사용
```ruby
@item.count                    # => 1000
@item.page(1).per(20).size        # => 20
@item.page(1).per(20).total_count # => 1000
```

**I18n and labels**
The default labels for 'first', 'last', 'previous', '…' and 'next' are stored in the I18n yaml inside the engine, and rendered through I18n API.
```yml
ko:
  views:
    pagination:
      first: '&laquo;'
      last: '&raquo;'
      previous: '&lsaquo;'
      next: '&rsaquo;'
      truncate: "&hellip;"
```

## Axlsx
xlsx spreadsheet generation with charts, images, automated column width, customizable styles and full schema validation. Axlsx helps you create beautiful Office Open XML Spreadsheet documents.
admin page에서 excel 추출
https://github.com/randym/axlsx

```ruby
respond_to do |format|
    format.xlsx do
        p = Axlsx::Package.new
        wb = p.workbook

        wb.add_worksheet(name: '제품리스트') do |sheet|
            sheet.add_row %w[ID 메인카테고리 서브카테고리
                             브랜드명 제품명 판매가 정상가 마진율 공급가
                             스타일1 스타일2 스타일3 조회수 아웃링크\ 클릭수 위시수]

            per_page = 40.0
            max_page = (@productions.length / per_page).ceil

            (1..max_page).each do |idx|
                @splitProductions = @productions.page(idx).per(per_page)

                @splitProductions.all.each do |production|
                    supplyCost = (production.selling_cost - production.selling_cost * production.margin_rate / 100).ceil

                    sheet.add_row [production.id, Constants::ProductionCategory.to_s(production.main_category), Constants::ProductionCategory.to_s2(production.main_category, production.sub_category),
                                   production.brand_name, production.name, production.selling_cost, production.cost, production.margin_rate, supplyCost,
                                   production.style1 == -1 ? '' : Constants::Style.to_s(production.style1),
                                   production.style2 == -1 ? '' : Constants::Style.to_s(production.style2),
                                   production.style3 == -1 ? '' : Constants::Style.to_s(production.style3),
                                   production.view_count, production.outbound_click_count, production.wish_count]
                end
            end
        end
        send_data p.to_stream.read, type: 'application/xlsx', filename: '전체제품_' + Time.now.to_i.to_s + '.xlsx'
    end
end
```

## Nokogiri
Nokogiri is an HTML, XML, SAX, and Reader parser. Among Nokogiri's many features is the ability to search documents via XPath or CSS3 selectors
다른 사이트에서 정보를 크롤링할 때 사용!
https://github.com/sparklemotion/nokogiri

```ruby
require 'nokogiri'
require 'open-uri'

if @item.artist_url.present?
    art_url = "https://twitter.com/" + @item.artist_url
    art_doc = Nokogiri::HTML(doc = open(art_url))
    @artist_name = art_doc.css("a.ProfileHeaderCard-nameLink")[0].content
    @artist_img = art_doc.css("img.ProfileAvatar-image")[0]['src']
end
```