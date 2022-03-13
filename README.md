 public Mono<Page<ShoppingCart>> getUserShoppingCarts(String email, Pageable pageable) {
        return userRepository.getUserByEmail(email).flatMap(user -> {
            Flux<ShoppingCart> shoppingCarts = shoppingCartRepository.getUserShoppingCartsPageable(user.getId(), pageable.getPageSize(), pageable.getOffset());
            shoppingCarts = shoppingCarts.flatMap(this::fillCartWithCartItems);
            Mono<Long> totalCartsCount = countUserShoppingCarts(user.getId());
            return shoppingCarts.collectList().flatMap(carts ->
                    totalCartsCount.flatMap(count -> Mono.just(new PageImpl<ShoppingCart>(carts, pageable, count))));
        });
    }



shopcards 是购物车的集合--》list
  list 进行map ， 购物车总数cartscount  
  {
  shopcard {
      card:{card1,card2,card3}
      page:
      count:10
  
    }
  }
  
