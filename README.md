# Different_types_query
```
public function brands()
   {
        $brands=BrandProvider::getApibrand2ForFashion();
        return response()->json([
            'brands'=>$brands
        ]);

   }
   public function banner()
   {
       $banner = BannerProvider::getApibanner2ForFashion();
       return response()->json([
        'banner'=>$banner
       ]);
   }
   public function slider1()
   {
        $sliders = SliderProvider::getAPISliderForFashion();
            return response()->json([
                'sliders' =>$sliders
                ]);
   }
   public function hotdeals()
   {
    
        // for  special_offer
        $hot_deals = Product::with(['multiImg'=>function($query)
      {
         $query->select('photo_name','product_id');
      }])->where('hot_deals', 1)
      ->where('ecom_name', '3')
      ->where('discount_price', '!=', NULL)
      ->select('id','product_thambnail','product_name','selling_price','discount_price','start_date','end_date')->orderBy('id', 'DESC')->limit(6)->get();
         return response()->json([
             'hot_deals' => $hot_deals
             ]);
   }
   public function latestproduct()
   {
       $latest_products = Product::orderBy('id', 'DESC')->where('ecom_name', '3')->limit(10)->get(['id','product_name','product_thambnail','selling_price','discount_price','unit','product_descp','product_size','product_color']);
       return response()->json([
           'latest_products'=>$latest_products
           ]);
   }
   public function fashiontoprated()
   {
        $top_rated = Product::select('id','product_name','product_thambnail','selling_price','discount_price')->has('review')
            ->withSum('review','quality')
            ->where('ecom_name', '3')
            ->orderBy('review_sum_quality','desc')->limit(10)
            ->get();
            return response()->json([
                'top_rated' => $top_rated
                ]);
   }
   public function popular_product(){
          $most_popular_all = Product::with('multiImg')->select('id','product_name','product_thambnail','selling_price','discount_price')->where('status', 1)->where('ecom_name', '3')->limit(15)->get();
          return response()->json([
              'most_popular_all' =>$most_popular_all
              ]);
   }
   public function fashion_products()
   {
        $products =DB::table('products')->select('id','product_thambnail','product_name','selling_price','discount_price','unit','product_descp','product_size','product_color')->where('status', 1)->where('ecom_name', '3')->orderBy('id', 'DESC')->limit(8)->get();
        return response()->json([
            'products' => $products
            ]);
   }
   public function fashion_topCategoriesThisWeek()
   {
        $topCategoriesThisWeek = Category::with('getSubCategoryLimitIs4')
           ->withCount('thisWeekOrdersProduct')
           ->where('ecom_id',3)
           ->orderBy('this_week_orders_product_count','desc')
           ->limit(4)
           ->get([]);
            return response()->json([
            'TopCategoriesThisWeek' => $topCategoriesThisWeek
            ]);
   }
   public function Best_Selling_Products()
   {
        $Best_Selling_Products = Order::where('orders.created_at', '>', Carbon::now()->subHours(12)->toDateTimeString())
                                ->where('orders.status', '=', 'delivered')
                                ->join('order_items', 'order_items.order_id', '=', 'orders.id')
                                ->join('products', 'products.id', '=', 'order_items.product_id')
                                ->join('reviews', 'products.id', '=', 'reviews.product_id')
                                ->select('order_items.product_id','products.*',DB::raw('sum(reviews.quality) as sum_star'),DB::raw('count(reviews.id) as total_star'))
                                ->groupBy('product_id')
                                ->where('products.ecom_name', '3')->limit(10)
                                ->get();
                                return response()->json([
                                    'Best_Selling_Products' =>$Best_Selling_Products
                                    ]);
   }
   public function special_offer()
   {
       // for  Spacial Offer
        $special_offers = Product::where('special_offer', 1)->where('ecom_name', '3')->orderBy('id', 'DESC')->limit(6)->get(['id','product_thambnail','product_name','selling_price','start_date','end_date','product_expire_date']);
        return response()->json([
            'special_offers' =>$special_offers
            ]);
    }
    public function fashion_special_deals()
    {
        $special_deals = Product::where('special_deals','1')->where('ecom_name', '3')->orderBy('id', 'DESC')->limit(6)->get(['id','product_thambnail','product_name','selling_price','start_date','end_date','product_expire_date']);
        return response()->json([
            'special_deals' =>$special_deals
            ]);
    }
    public function fashion_hot_deals()
    {
        $hot_deals = Product::with(['multiImg'=>function($q)
        {
          $q->select('photo_name','product_id');
        }])->where('ecom_name', '3')->where('discount_price', '!=', NULL)->orderBy('id', 'DESC')->get(['id','product_name','start_date','end_date','product_expire_date','selling_price','discount_price']);
        return response()->json([
            'hot_deals'=> $hot_deals
            ]);
    }
     public function FashionIndex()
    {
        // for  brands


        // for  special_deals
        $special_deals = Product::where('special_deals', 1)->where('ecom_name', '3')->orderBy('id', 'DESC')->limit(6)->get();

        // for  Spacial Offer
        $special_offers = Product::where('special_offer', 1)->where('ecom_name', '3')->orderBy('id', 'DESC')->limit(6)->get();
        // for  hot_deals
        $hot_deals = Product::with('multiImg')->where('hot_deals', 1)->where('ecom_name', '3')->where('discount_price', '!=', NULL)->orderBy('id', 'DESC')->limit(6)->get();
        // for featured
        $featureds = Product::where('featured', 1)->where('ecom_name', '3')->orderBy('id', 'DESC')->limit(6)->get();
        // for product
        $products = Product::where('status', 1)->where('ecom_name', '3')->orderBy('id', 'DESC')->limit(8)->get();
        // for new product
        $newTwoproducts = Product::where('status', 1)->where('ecom_name', '3')->orderBy('id', 'DESC')->limit(2)->get();
        // for slider
        // for slider

        $categories = Category::with('fashionProduct')->where('ecom_id', '3')->get();

        // home page banner-category
        $bannerCategroy = BannerCatagory::where('status', 1)->where('ecom_id', '3')->orderBy('id', 'DESC')->limit(3)->get();


        $todayDate = Carbon::now();
        $dailyBestSales =  DB::table('order_items')
            ->join('products', 'products.id', '=', 'order_items.product_id')
            ->join('reviews', 'products.id', '=', 'reviews.product_id')
            ->select('order_items.product_id','products.*',DB::raw('sum(reviews.quality) as sum_star'),DB::raw('count(reviews.id) as total_star'))
            ->where('products.ecom_name', '3')
            ->groupBy('product_id')
            ->orderBy('total_star', 'DESC')
            ->limit(16)
            ->get();

        $deliverdProducts =  OrderItem::select('product_id')->groupBy('product_id')
            ->join('orders', 'order_items.order_id', '=', 'orders.id')
            ->join('products', 'products.id', '=', 'order_items.product_id')
            ->where('products.ecom_name', '3')
            ->where('orders.status', 'delivered')
            ->get(['products.*']);


    // Dialy Best Sell Product




        //   latest discount products
        $latestdiscountproduct = Product::whereNotNull('discount_price')->where('ecom_name', '3')->orderBy('id', 'DESC')->limit(2)->get();
        $trendingProducts = DB::table('order_items')
            ->join('products', 'products.id', '=', 'order_items.product_id')
            ->where('products.ecom_name', '3')
            ->select('*', 'product_id', DB::raw('count(*) as total'))
            ->groupBy('product_id')
            ->orderBy('total', 'DESC')
            ->limit(3)
            ->get();

        $topCategoriesThisWeek = Category::with('getSubCategoryLimitIs4')
           ->withCount('thisWeekOrdersProduct')
           ->where('ecom_id',3)
           ->orderBy('this_week_orders_product_count','desc')
           ->limit(4)
           ->get();
```
